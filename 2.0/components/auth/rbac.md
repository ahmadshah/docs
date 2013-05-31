---
layout: default
title: Using Role Based Access Control

---

Using Role Based Access Control
==============

Orchestra Platform Role Based Access Control gives you the ability to create custom ACL metrics which is unique to each of your extensions. 

In most other solutions, you are either restrict to file based configuration for ACL or only allow to define a single metric for your entire application. This simplicity would later become an issue depends on how many extensions do you have within your application.

* [Creating a New ACL Instance](#creating)
* [Verifying the ACL](#verifying)

<article id="creating">
## Creating a New ACL Instance

	<?php
	
    Orchestra\Acl::make('acme')->attach(Orchestra\App::memory());

Imagine we have a **acme** extension, above configuration is all you need in your extension start file.

> Using `attach()` allow the ACL to utilize `Orchestra\Memory` to store the metric so we don't have to define the ACL in every request.

</article>

<article id="verifying">
## Verifying the ACL

To verify the created ACL, you can use the following code.

	$acl = Orchestra\Acl::make('acme');
	
	if ( ! $acl->can('manage acme')) 
	{
		return Redirect::to(
			handles('orchestra/foundation::login')
		);
	}
	
Or you can create a route filter.

	Route::filter('foo.manage', function ()
	{
		if ( ! Orchestra\Acl::make('acme')->can('manage acme'))
		{
			return Redirect::to(
				handles('orchestra/foundation::login')
			);
		}
	});

</article>

<article id="migration-example">
## Migration Example

Since an ACL metric is defined for each extension, it is best to define ACL actions using a migration file.

	<?php
	
	use Illuminate\Database\Migrations\Migration;

	class FooDefineAcl extends Migration {
		
		/**
	 	 * Run the migrations.
	     *
	 	 * @return void
	 	 */
		public function up()
		{
			$role = Orchestra\Model\Role::admin();
			$acl  = Orchestra\Acl::make('acme');
			
			$actions = array(
				'manage acme',
				'view acme',
			);

			$acl->actions()->fill($actions);
			$acl->roles()->add($role->name);
			
			$acl->allow($role->name, $actions);
		}
		
		/**
	 	 * Reverse the migrations.
	     *
	 	 * @return void
	 	 */
		public function down()
		{
			// nothing to do here.
		}
	
	}