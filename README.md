# Customized wordpress RSS feed for Twitter

This is RSS feed specifically designed to post to twitter. Generated tags help increase tweet visibility.

How is this feed customized for twitter? 

* Article tags converted to hashtag links. Basically looped through tags, removed spaces and appended hashtag symbol;
- ToDo:
    - Limit title characters;
    - Prioritize showing featured images instead of adding link;
    - Remove unsupported symbols from tags;
    - ...

## Tweet sample

![Image of Twitter Sample](https://github.com/mksolemn/wp-xml-rss-feed-for-twitter/blob/master/assets/twitter-sample.png?raw=true)

I'm using hootsuite's RSS autopublishing tool - it's free and takes off some load of my server. All wordpress plugins kept breaking my site or posted duplicate content to twitter... not a fan.

# Setup

#1
Navigate to your theme directory and open functions.php file
path to file: /wp-content/themes/[name-of-your-theme]/functions.php

#2
Paste following code to the bottom of the file:
```php
add_action('init', 'customRSS');
function customRSS(){
        add_feed('NAME-OF-YOUR-FEED', 'customRSSFunc');
}
function customRSSFunc(){
        get_template_part('rss', 'NAME-OF-YOUR-FEED');
}
```

#3
In same directory create file. File must follow specific naming structure ([rss]-[feedname].php), for current example it would be: rss-name-of-your-feed.php;

```php
<?php
/**
 * Template Name: Custom RSS Template - Feedname
 */
$postCount = 5; // The number of posts to show in the feed
$posts = query_posts('showposts=' . $postCount);
header('Content-Type: '.feed_content_type('rss-http').'; charset='.get_option('blog_charset'), true);
echo '<?xml version="1.0" encoding="'.get_option('blog_charset').'"?'.'>';
?>
<rss version="2.0"
        xmlns:content="http://purl.org/rss/1.0/modules/content/"
        xmlns:wfw="http://wellformedweb.org/CommentAPI/"
        xmlns:dc="http://purl.org/dc/elements/1.1/"
        xmlns:atom="http://www.w3.org/2005/Atom"
        xmlns:sy="http://purl.org/rss/1.0/modules/syndication/"
        xmlns:slash="http://purl.org/rss/1.0/modules/slash/"
        <?php do_action('rss2_ns'); ?>>
<channel>
        <title><?php bloginfo_rss('name'); ?> - Feed</title>
        <atom:link href="<?php self_link(); ?>" rel="self" type="application/rss+xml" />
        <link><?php bloginfo_rss('url') ?></link>
        <description><?php bloginfo_rss('description') ?></description>
        <lastBuildDate><?php echo mysql2date('D, d M Y H:i:s +0000', get_lastpostmodified('GMT'), false); ?></lastBuildDate>
        <language><?php echo get_option('rss_language'); ?></language>
        <sy:updatePeriod><?php echo apply_filters( 'rss_update_period', 'hourly' ); ?></sy:updatePeriod>
        <sy:updateFrequency><?php echo apply_filters( 'rss_update_frequency', '1' ); ?></sy:updateFrequency>
        <?php do_action('rss2_head'); ?>
        <?php while(have_posts()) : the_post(); ?>
                <item>
                        <title>
						<?php
the_title_rss();
   $tags = get_the_tags();

    foreach($tags as $tag) {
$tagName = $tag->name;
$tagName = preg_replace('/\s+/', '', $tagName);
        echo " #$tagName";
   }
						?></title>
                        <link><?php the_permalink_rss(); ?></link>
                        <pubDate><?php echo mysql2date('D, d M Y H:i:s +0000', get_post_time('Y-m-d H:i:s', true), false); ?></pubDate>
                        <dc:creator><?php the_author(); ?></dc:creator>
                        <guid isPermaLink="false"><?php the_guid(); ?></guid>
                        <description><![CDATA[<?php the_excerpt_rss() ?>]]></description>
                        <content:encoded><![CDATA[<?php the_excerpt_rss() ?>]]></content:encoded>
                        <?php rss_enclosure(); ?>
                        <?php do_action('rss2_item'); ?>
                </item>
        <?php endwhile; ?>
</channel>
</rss>
```
