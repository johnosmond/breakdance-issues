# Breakdance Issues
Some code and instuctions for correcting or finding work-around for some issues with the Breakdance WordPress Builder.

I like the Breakdance builder, but there are some issues with it. Recently, I've figured out how to correct some of them and would like to share my findings with you here.

## Issues

Issue #1: If you build your menus with the BD Menu Builder and include drop-down menus, the 'active' settings do not apply to the items in the drop-down menu.

Issue #1.1 (because it's related): The 'active' settings also do not bubble up to the parent when a submenu page is active.

To be clear, the 'active' settings apply to the top-level menu item when that page is the active page, but when any of the items in the drop-down is the active page, neiter the item in the drop-down nor the parent at the top get 'active' settings applied to them.

Issue #2: The mobile menu does not allow the top-level item of a drop-down menu to click through to it's linked page. When in mobile view, the top-level becomes a selector only.

## Preparations

In order to solve these issues you will need a child theme. Breakdance recommends disabling the theme in the BD settings. If you do keep your theme they recommend using the 'Breakdance Zero Theme'. That is what we will do.

So, in preparation follow these steps:
* Change the settings in BD to 'Keep Theme'
* Upload and activate the BD Zero Theme
* Open your application in VS Code (or another editor of your choice)
* Create a new folder called 'js' (optional of course)
* Create a new file called 'menu.js'

We'll use the 'menu.js' file and your 'functions.php' file in the solutions.

## Solutions/Work Arounds

Here are my solutions to these issues.

### Issue #1

(This one's easy)

Add this CSS to the custom CSS for the menu builder:
.breakdance-dropdown-item--active .breakdance-dropdown-link__label {
  color: red;
}

In other words, select the 'Menu Builder' in the structure. Then select the 'Advanced' tab. In the 'Custom CSS' block paste the code above.

That should make the items in the drop-down menu highlight in red (or whatever color you want).

### Issue #1.1 

(This one is a bit harder.)

Open the 'menu.js' file you created in VS Code and paste this code:

// change the color of the top-level link in the dropdown menu 
// if the current URL matches the URL of a submenu item
document.addEventListener('DOMContentLoaded', function() {
    // Get the current URL of the page
    const currentUrl = window.location.href;

    // Find all the dropdown menus that contain submenu items
    const dropdownMenus = document.querySelectorAll('.breakdance-dropdown');

    dropdownMenus.forEach(dropdown => {
        // Get the top-level link of the dropdown
        const topLevelLink = dropdown.querySelector('.breakdance-dropdown-toggle > a');
        
        // Get all submenu links
        const submenuLinks = dropdown.querySelectorAll('.breakdance-dropdown-link');

        // Check each submenu link to see if its href matches the current URL
        submenuLinks.forEach(link => {
            if (currentUrl.includes(link.href)) {
                // If a submenu link matches the current URL, change the top-level link color to red
                topLevelLink.style.color = 'red';
            }
        });
    });
});

Then open your 'functions.php' file for the Zero Theme and paste this at the bottom:

// added script for menu customization
function my_custom_scripts() {
    wp_enqueue_script('jquery');
    wp_enqueue_script('my-custom-js', get_template_directory_uri() . '/js/menu.js', array('jquery'), null, true);
}
add_action('wp_enqueue_scripts', 'my_custom_scripts');
