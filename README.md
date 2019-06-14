# Add-Dynamic-download-link-by-category-using-ACF-wordpress
Add Dynamic download link by category using ACF in wordpress


1. First of you need to Create a custom post type if you need OR You can use wordpress post.(Add code in functions.php)

<pre>
add_action( 'init', 'register_custom_post_type_downloadpdf' );
 
function register_custom_post_type_downloadpdf() {
 
    $labels = array(
        'menu_name'             => 'Downloads PDF',
        'name'                  => 'Downloads PDF',
        'singular_name'         => 'Download PDF',
        'add_new'               => 'Add new Download',
        'add_new_item'          => 'Add new Download',
        'edit_item'             => 'Edit Download',
        'new_item'              => 'Add Download',
        'view_item'             => 'Show Download',
        'search_items'          => 'Search Download(s)',
        'not_found'             => 'No Download(s) found',
        'not_found_in_trash'    => 'No Download(s) found in trash',
        'parent_item_colon'     => 'Current Download:',
    );
 
    $args = array(
        'labels'                => $labels,
        'hierarchical'          => true,
        'supports'              => array( 'revisions', 'title', 'editor','custom-fields'),
        'public'                => true,
        'show_ui'               => true,
        'show_in_menu'          => true,
        'menu_position'         => 5,
        'menu_icon'             => 'dashicons-admin-site',
        'show_in_nav_menus'     => true,
        'publicly_queryable'    => true,
        'exclude_from_search'   => false,
        'query_var'             => true,
        'can_export'            => true,
        'rewrite'               => true,
        'has_archive'           => 'downloadpdf',
        'capability_type'       => 'post',
    );
 
    register_post_type( 'downloadpdf', $args );
}

add_action( 'init', 'taxonomy_downloads', 0 );
 
function taxonomy_downloads() {
 
    $tax_labels = array(
        'menu_name'             => 'Categories',
        'name'                  => 'Categories',
        'singular_name'         => 'Category',
        'search_items'          => 'Search Categories',
        'popular_items'         => 'Popular Categories',
        'all_items'             => 'All Category',
        'edit_item'             => 'Edit Category', 
        'update_item'           => 'Update Category',
        'add_new_item'          => 'Add new Category',
        'new_item_name'         => 'New Category',
        'add_or_remove_items'   => 'Add or remove Categories',
        'not_found'             => 'No Categories found.',
      );
     
      register_taxonomy('categories',array('downloadpdf'), array(
        'labels'                => $tax_labels,
        'hierarchical'          => true,
        'show_ui'               => true,
        'show_admin_column'     => false,
        'show_in_nav_menus'     => true,
        'query_var'             => true,
        'rewrite'               => array( 'slug' => 'downloadpdf/categories' ),
      ));
}
</pre>

2. Install Advanced Custom Fields Wordpres Plugin : https://wordpress.org/plugins/advanced-custom-fields/

3. Create a custom field in ACF

![Advance-custom-field](https://user-images.githubusercontent.com/16508401/59488800-25b35f00-8e9e-11e9-9ca0-a8cb86e1b372.png)

4.Shortcode genrate for Get Post by category(Add code in functions.php)

<pre>
/*
	Add shortcode for Downloads
*/

function download_shortcode( $atts ){ 
	$category = $atts['category'];

	if(isset($category) && !empty($category)){
    	$tax_query = array('taxonomy' => 'categories', 'field' => 'id', 'terms' => $category); 
  	}


	$args = array( 
			'post_type' => 'downloadpdf',
			'orderby' => 'title',
			'order'   => 'ASC',
			'post_status' => 'publish',
			'posts_per_page' => -1,       
			'tax_query' => array(
			'relation' => 'OR',
			$tax_query,
		)
	);


	$arr_posts = new WP_Query( $args );	
	if( $arr_posts->have_posts() ) :

    	while( $arr_posts->have_posts() ): $arr_posts->the_post();
    		$download_link =  get_field('download_link');

		 	echo $download_link;
     		echo get_the_title(); 
     	endwhile;
    	
    	wp_reset_postdata();
  	else :
    	echo 'No posts found';
  	endif;
  }
add_shortcode( 'download_posts', 'download_shortcode' );	
</pre>


5. Add shortcode in page
<pre>
[download_posts category="29"]
</pre>
