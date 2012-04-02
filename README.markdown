# A little Ajax-driven article reader built with the Drupal Ajax framework
===========================================================================

This code demonstrates a simple use case.  

It lists article titles and article thumbnails on one side of a webpage and a 
full article in the main content of the webpage.

Click on an article's title or tumbnail in the list and the article's full 
content will dynamically load in the main content area.

I built this code for a screencast which you may view here: [tbd]

There are two modules in this repo.  The reader module titled, "ajax_reader" and
a feature module titled, "ajax_demo" which contains the views and content type 
used in the demo.

I may later add to the repo an out of the box code set with the theme and 
databse ready to run. 

Below you will find the initial script for the screencast to give you an idea 
of its content.

## INTRO

[Reader app up in background]

Hello.  My name is Sean Buscay.  

Today in this short screencast I'll show you how to use Drupal 7's built-in 
Ajax Framework to dynamically update web page content with new data from the 
server; all without a page reload.

## WHAT

I've built a little Ajax-driven article reader to show you how the Drupal Ajax 
framework works. This is just a simple use-case. 

The reader loads article thumbnails on the left side of the page along with 
the article titles. 

[Click on articles in the left side bar] 

Click on one of the article images or titles and the full article will 
dynamically load in the larger right hand side of the page. All this happens by 
pulling the new node content from the server through Ajax and changing the 
content area's HTML.  This is accomplished without the end-user having to see a 
reload of the full page in the browser. 

[Turn off Javascript support for the web browser]

For the edge case when the site visitor (human or search bot) visits the web page 
without JavaScript enabled, the system will gracefully degrade. A click on the 
article thumbnail or title will take the visitor to the URL of the article and 
load the page via a standard HTML request. 

[Click a couple of articles again and highlight the URL has changed]

## HOW

Now let's see how I built the reader. 

[Switch to code view]

### Code

For this Ajax reader demo I've written a small module and given it the name of, 
"Ajax Reader". Right now, we are looking at the module's code.  

I'll deconstruct the code for you function by function. Don't worry.  
It's pretty basic and really comes down to just a four main parts. 

#### function ajax_reader_init()

    function ajax_reader_init() {
      drupal_add_js('misc/jquery.form.js');
      drupal_add_library('system', 'drupal.ajax');
    }
    
#### function ajax_reader_menu()

    function ajax_reader_menu() {
      // A menu callback is required when using ajax outside of the Form API.
      $items['ajax-reader'] = array(
        'page callback' => 'ajax_link_response',
        'access callback' => 'user_access',
        'access arguments' => array('access content'),
        'type' => MENU_CALLBACK,
      );
      return $items;
    }

#### function ajax_link_response($type = 'ajax', $nid = 0)

    function ajax_link_response($type = 'ajax', $nid = 0) {
      $output = _ajax_reader_load_noder($nid);
      if ($type == 'ajax') {
        $commands = array();
        $commands[] = ajax_command_replace('#content', '<div id="content">' . $output . '</div>');
        $page = array(
          '#type' => 'ajax',
          '#commands' => $commands
        );
        ajax_deliver($page);
      }
      else {
        $output = '<div id="content">' . $output . '</div>';
        return $output;
      }
    }


#### function _ajax_reader_load_noder($nid = 0) 

    function _ajax_reader_load_noder($nid = 0) {
      $node = node_load($nid, NULL, false);
      if ($node) {
        $vnode = node_view($node);
        return theme("node", $vnode);
      }
     // return views_embed_view ('front','page_1', $nid);
    }

### Link Structure

For the last part of this system we need to format the links to the menu 
callback's menu item which we mapped to the page call back function.

To use the Ajax framework, the structure of the links to the menu callback 
should follow this format ... [Demo on screen]

    <a href="/[menu callback menu item]/nojs/arguments" class="use-ajax">Link Title</a>

    <a href="http://reader.dev/ajax-reader/nojs/259" class="use-ajax">Causa Dolore Lucid...</a>


In the view which generates the article teasers block on the left side of the 
reader page, I've re-written the URLs like so...

[Show the Drupal view and the re-write URL setup.]

### Return a View instead of a content node

Let's make a quick change to this last function to dynamically load a view into 
the readers's content rather than a full node.

This is just a view I've created which receives a node id as its argument and 
then displays field data from the node.  

[After changing function _ajax_reader_load_noder]

Now when we click on the article thumbnail or title, the view's content loads on
 the right hand side of the page. 

For more details on the views and the other components of the reader, you can visit: 

http://seanbuscay.com/ajax-reader-demo 

Or get all the code on github at https://github.com/seanbuscay/drupal-ajax-demo 

You'll find extra reference material and resources at both URLs.

Just remember, there are really only four basic steps to setup and use 
Drupal 7s Ajax-framework for non-form based content updates. 

1. write a menu callback 
2. return Ajax formatted content in a page callback function 
3. format the links (add "no-js" & "user-ajax" to the links that point to the menu callback 
4. make sure the "jquery.form.js" and the "drupal.ajax" libraries are loaded in the page

That's the end of the screencast. I hope it was helpful. Thank you for watching.  

## CREDITS

It was Katherine Bailey (katbailey)in this presentation  http://chicago2011.drupal.org/sessions/drupal-7-javascript-developers that really helped me get a handle on the steps to using Non-form based Ajax in D7.

Here's a link to the slides.  Kat co-presented with Randy Fay.  It's her parts of the presentation that I refered to for this code.

She put the code for her demo here: http://katbailey.net/resources/d7js
