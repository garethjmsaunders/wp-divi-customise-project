# Customise Divi project custom post type

## Summary

Change the WordPress Divi theme projects custom post type to anything you want.

This README originally appeared as a blog post: [Changing the Divi projects custom post type to anything you want](http://blog.garethjmsaunders.co.uk/2015/03/07/changing-the-divi-projects-custom-post-type-to-anything-you-want/).


## Description

I developed this code while building a website for a friend, using the [Divi theme from Elegant Themes](http://www.elegantthemes.com/gallery/divi/). The website was for a holiday property letting company so **I changed the built-in Projects content type to Properties**. 

This documentation walks you through what I did, and how **you can change the Project custom post type to anything you want**.


### The problem

Divi is a great theme to use: it’s very flexible, it’s responsive (so it works equally well on smartphones as well as huge desktop monitors), and it has the easiest, drag-and-drop editor that I’ve ever used for WordPress.

Divi comes with a built in content type called **Projects**; WordPress calls them ‘custom post types’. I use this content type on [my own website](http://www.garethjmsaunders.co.uk/projects/) to list the various projects that I’ve been involved in over the years.

Om the WordPress admin menu ‘Projects’ appears on the list beneath Posts, Media, Pages, and Comments:

Divi also ships with a number of attractive ways to display your projects using its Portfolio and Filtered Portfolio modules. You can even display these full-width or as a grid, such as this:

These are exactly the features that I’d like to use on the property letting website:

* Keep properties separate from pages and posts, using a custom post type.
* Display all properties in a grid.
* Allow users to filter properties based on the categories that are assigned to them.

So, I want all the features of Divi’s built-in Projects custom post type, but I don’t want them to be called Projects. I want them to be called Properties.


### Use a child theme

First, I strongly recommend that you use a child theme when customising Divi (or indeed any other WordPress theme). A child theme inherits the functionality and styling of another theme, called the parent theme, and allows you to make local customisations to it which will not be overwritten when the theme updates.

Elegant Themes have a useful walkthrough on [how to create a child theme, and why you should be using one](http://www.elegantthemes.com/blog/resources/wordpress-child-theme-tutorial).

The WordPress Codex also has useful information about [child themes](http://codex.wordpress.org/Child_Themes).


### How to do it

This very useful post on the Elegant Tweaks blog: “[Change Divi Projects URL-permalink](http://www.eleganttweaks.com/divi/change-divi-projects-url-permalink/)” got me started, and about 95% of the way.

I copied the code, added it to the functions.php file in my child theme, and set about editing it.


#### 1. remove_action / add_action

In a nutshell the code from Elegant Tweaks does two things:

1. It defines a new function — called `child_et_pb_register_posttypes()` — that will redefine the characteristics of the Projects content type.
2. It removes the default Projects custom post type contained in Divi, and replaces it with our one in the child theme.

This last point, I believe, is simply to be tidy: rather than clumsily overwriting the existing ‘project’ custom post type it gracefully removes the old one, and creates a redefined version in its place.


#### 2. Labels

In that Elegant Themes post the author was only concerned with changing the URL from /projects/ to /photos/. So in his example, the names used in the WordPress admin screens still referred to projects: Edit Project, Add New Project, etc. But I want to change these too.

In the code for a custom post type these are referred to as ‘labels’ and are defined in the $labels array. This is what my code looks like now:

```
<?php function child_et_pb_register_posttypes() { $labels = array( 'add_new' => __( 'Add New', 'Divi' ),
        'add_new_item'       => __( 'Add New Property', 'Divi' ),
        'all_items'          => __( 'All Properties', 'Divi' ),
        'edit_item'          => __( 'Edit Property', 'Divi' ),
        'menu_name'          => __( 'Properties', 'Divi' ),
        'name'               => __( 'Properties', 'Divi' ),
        'new_item'           => __( 'New Property', 'Divi' ),
        'not_found'          => __( 'Nothing found', 'Divi' ),
        'not_found_in_trash' => __( 'Nothing found in Trash', 'Divi' ),
        'parent_item_colon'  => '',
        'search_items'       => __( 'Search Properties', 'Divi' ),
        'singular_name'      => __( 'Property', 'Divi' ),
        'view_item'          => __( 'View Property', 'Divi' ),
    );
```

As you can see, something I find useful is to list the elements alphabetically. Personally, I find it easier to work this way; your mileage may vary.

Obviously, if you are customising this for your own requirements simply edit this to reflect your needs.


#### 3. Custom post type options

Next, we define the arguments to be passed to the register_post_type function. These define not only how the custom post type is used but also how it is displayed in the WordPress admin menu: where it sits and what icon it uses.

##### Slug

The most important option here, for our purpose of customising it, is the 'slug' key. You must set its value (in single quotes) to whatever you need it to be. In my case 'slug' => 'property'. I’ve highlighted this in the snippet below.

Just make sure you don’t set the slug to the same name as an existing page.

##### Menu icon and position

One useful new addition to the code provided by Elegant Tweaks are the options to set the menu icon and where it sits on the menu.

As these are properties I decided to use the [home dashicon](https://developer.wordpress.org/resource/dashicons/#admin-home).

I also decided to move it up a bit, from beneath Comments to immediately below Posts. WordPress uses numbers to specify where custom post types should sit, e.g.

* 5 — below Posts (this is where I want it to appear)
* 10 — below Media
* 15 — below Links
* 20 — below Pages
* 25 — below Comments

[A full list](http://codex.wordpress.org/Function_Reference/register_post_type) can be found in the WordPress Codex.

So, here is the code I now have:

```
$args = array(
    'can_export'         => true,
    'capability_type'    => 'post',
    'has_archive'        => true,
    'hierarchical'       => false,
    'labels'             => $labels,
    'menu_icon'          => 'dashicons-admin-home',
    'menu_position'      => 5,
    'public'             => true,
    'publicly_queryable' => true,
    'query_var'          => true,
    'show_in_nav_menus'  => true,
    'show_ui'            => true,
    'rewrite'            => apply_filters(
        'et_project_posttype_rewrite_args', array(
        'feeds'          => true,
        'slug'           => 'property',
        'with_front'     => false,
    )),
    'supports'           => array( 'title', 'editor', 'thumbnail', 'excerpt', 'comments', 'revisions', 'custom-fields' ),
);
```


#### Register the post type

The next line now does the grunt work and registers this custom post type with WordPress.

```
register_post_type( 'project', apply_filters(
    'et_project_posttype_args', $args )
);
```

This tells WordPress to apply all of these options to the ‘project’ custom post type.

Because we are redefining this existing custom post type (by changing the URL, the menu labels, the menu icon and position) it means that everything else (the default project page layouts and portfolio modules) will work as expected without any further customization.

#### Categories and tags

The rest of the code I left untouched. This code defines the categories and tags to be used with the projects/properties custom post type.


### Complete code

Here is the full code that I have in my child theme’s functions.php file:

```
<?php function child_et_pb_register_posttypes() { $labels = array( 'add_new' => __( 'Add New', 'Divi' ),
        'add_new_item'       => __( 'Add New Property', 'Divi' ),
        'all_items'          => __( 'All Properties', 'Divi' ),
        'edit_item'          => __( 'Edit Property', 'Divi' ),
        'menu_name'          => __( 'Properties', 'Divi' ),
        'name'               => __( 'Properties', 'Divi' ),
        'new_item'           => __( 'New Property', 'Divi' ),
        'not_found'          => __( 'Nothing found', 'Divi' ),
        'not_found_in_trash' => __( 'Nothing found in Trash', 'Divi' ),
        'parent_item_colon'  => '',
        'search_items'       => __( 'Search Properties', 'Divi' ),
        'singular_name'      => __( 'Property', 'Divi' ),
        'view_item'          => __( 'View Property', 'Divi' ),
    );
 
    $args = array(
        'can_export'         => true,
        'capability_type'    => 'post',
        'has_archive'        => true,
        'hierarchical'       => false,
        'labels'             => $labels,
        'menu_icon'          => 'dashicons-admin-home',
        'menu_position'      => 5,
        'public'             => true,
        'publicly_queryable' => true,
        'query_var'          => true,
        'show_in_nav_menus'  => true,
        'show_ui'            => true,
        'rewrite'            => apply_filters( 'et_project_posttype_rewrite_args', array(
            'feeds'          => true,
            'slug'           => 'property',
            'with_front'     => false,
        )),
        'supports'           => array( 'title', 'editor', 'thumbnail', 'excerpt', 'comments', 'revisions', 'custom-fields' ),
    );
 
    register_post_type( 'project', apply_filters( 'et_project_posttype_args', $args ) );
 
    $labels = array(
        'name'              => _x( 'Categories', 'Property category name', 'Divi' ),
        'singular_name'     => _x( 'Category', 'Property category singular name', 'Divi' ),
        'search_items'      => __( 'Search Categories', 'Divi' ),
        'all_items'         => __( 'All Categories', 'Divi' ),
        'parent_item'       => __( 'Parent Category', 'Divi' ),
        'parent_item_colon' => __( 'Parent Category:', 'Divi' ),
        'edit_item'         => __( 'Edit Category', 'Divi' ),
        'update_item'       => __( 'Update Category', 'Divi' ),
        'add_new_item'      => __( 'Add New Category', 'Divi' ),
        'new_item_name'     => __( 'New Category Name', 'Divi' ),
        'menu_name'         => __( 'Categories', 'Divi' ),
    );
 
    register_taxonomy( 'project_category', array( 'project' ), array(
        'hierarchical'      => true,
        'labels'            => $labels,
        'show_ui'           => true,
        'show_admin_column' => true,
        'query_var'         => true,
    ) );
 
    $labels = array(
        'name'              => _x( 'Tags', 'Property Tag name', 'Divi' ),
        'singular_name'     => _x( 'Tag', 'Property tag singular name', 'Divi' ),
        'search_items'      => __( 'Search Tags', 'Divi' ),
        'all_items'         => __( 'All Tags', 'Divi' ),
        'parent_item'       => __( 'Parent Tag', 'Divi' ),
        'parent_item_colon' => __( 'Parent Tag:', 'Divi' ),
        'edit_item'         => __( 'Edit Tag', 'Divi' ),
        'update_item'       => __( 'Update Tag', 'Divi' ),
        'add_new_item'      => __( 'Add New Tag', 'Divi' ),
        'new_item_name'     => __( 'New Tag Name', 'Divi' ),
        'menu_name'         => __( 'Tags', 'Divi' ),
    );
 
    register_taxonomy( 'project_tag', array( 'project' ), array(
        'hierarchical'      => false,
        'labels'            => $labels,
        'show_ui'           => true,
        'show_admin_column' => true,
        'query_var'         => true,
    ) );
 
    $labels = array(
        'name'               => _x( 'Layouts', 'Layout type general name', 'Divi' ),
        'singular_name'      => _x( 'Layout', 'Layout type singular name', 'Divi' ),
        'add_new'            => _x( 'Add New', 'Layout item', 'Divi' ),
        'add_new_item'       => __( 'Add New Layout', 'Divi' ),
        'edit_item'          => __( 'Edit Layout', 'Divi' ),
        'new_item'           => __( 'New Layout', 'Divi' ),
        'all_items'          => __( 'All Layouts', 'Divi' ),
        'view_item'          => __( 'View Layout', 'Divi' ),
        'search_items'       => __( 'Search Layouts', 'Divi' ),
        'not_found'          => __( 'Nothing found', 'Divi' ),
        'not_found_in_trash' => __( 'Nothing found in Trash', 'Divi' ),
        'parent_item_colon'  => '',
    );
 
    $args = array(
        'labels'             => $labels,
        'public'             => false,
        'can_export'         => true,
        'query_var'          => false,
        'has_archive'        => false,
        'capability_type'    => 'post',
        'hierarchical'       => false,
        'supports'           => array( 'title', 'editor', 'thumbnail', 'excerpt', 'comments', 'revisions', 'custom-fields' ),
    );
 
    register_post_type( 'et_pb_layout', apply_filters( 'et_pb_layout_args', $args ) );
}
 
function remove_et_pb_actions() {
    remove_action( 'init', 'et_pb_register_posttypes', 15 );
}
 
add_action( 'init', 'remove_et_pb_actions');
add_action( 'init', 'child_et_pb_register_posttypes', 20 );
?>
```

## Notes

Like many things on my blog I’m primarily putting it here for my own reference, but if you find it useful — or would like to suggest improvements or additional features — please leave a comment below.

### Remember to reset your permalinks

Sometimes WordPress gets a bit muddled when you play around with custom post types.

The way to fix this is to go to **Settings > Permalinks > Save Changes**.

That’s enough to flush the permalinks and your custom post type should work. I had to do that a couple of times while figuring out how to do this.

### Plugin discovery

The [Divi Page Building for Custom Content Types](http://laternastudio.com/blog/divi-page-builder-for-custom-post-types) plugin allows you to use the Divi builder for Posts, or indeed any custom post type.

### Fix for Divi 2.5

I’ve now (finally) updated the code to make it work fully in Divi 2.5.

In the end it was quite simple: the `add_action(...)`` and `remove_action(...)`` priorities were wrong in my code. These tell WordPress in which order actions should be executed.

In my previous code I was instructing WordPress to unload the default Divi project custom post type before it had even been defined.

The default priority value is `10`; I’d set mine to `0` and `1`, respectively.

A huge thank you to Craig Campbell for his excellent [Chrome Logger](https://craig.is/writing/chrome-logger) extension and [ChromePHP class](https://github.com/ccampbell/chromephp) — two tools that greatly helped me work out what was going on.


## License

This code is licensed under the GPL v3 or later.

> This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

> This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

> You should have received a copy of the GNU General Public License along with this program.  If not, see http://www.gnu.org/licenses/.


## Credits

Developed by [Gareth J M Saunders](http://www.garethjmsaunders.co.uk).