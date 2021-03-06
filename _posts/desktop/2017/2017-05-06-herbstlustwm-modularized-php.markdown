---
layout: post
title:  "Modularized HerbstluftWM in PHP"
date:   2017-05-06 17:35:15 +0700
categories: desktop
tags: [coding, php, herbstluftwm]
author: epsi

excerpt:
  Doing Hersbtluft WM Config using PHP.
  
related_link_ids: 
  - 17050135  # HerbstluftWM Overview
  - 17050235  # HerbstluftWM BASH
  - 17050335  # HerbstluftWM Perl
  - 17050435  # HerbstluftWM Python
  - 17050535  # HerbstluftWM Ruby
  - 17050635  # HerbstluftWM PHP
  - 17050735  # HerbstluftWM Lua
  - 17050835  # HerbstluftWM Haskell

---

### Preface

> Goal: Separate Main Flow, Code, and Data.

So anyone can focus to alter special customization in Main Script,
without changing the whole stuff.

### Which PHP

Since there many PHP version.
I should say that I use PHP7.
But don't worry, it is portable.
I do not use any sophisticated code.

#### Reading

Before you jump off to scripting,
you might desire to read this overview.

*	[Modularized HerbstluftWM Overview][local-overview]

#### All The Source Code:

Impatient coder like me, like to open many tab on browser.

*	[gitlab.com/.../dotfiles/.../php/][dotfiles-php-directory]

-- -- --

{% include post/2017/05/herbstlustwm-modularized-language.md %}

-- -- --

### Directory Structure

Directory Structure has been explained in preface. 
This figure will explain how it looks 
in <code>PHP script</code> directory.

![HerbstluftWM: Directory Structure][image-php-01-tree]{: .img-responsive }

-- -- --

### Modularizing in PHP

	Nothing to say here. PHP is simple

#### Declare a module

No need to explicitly define what to export.

	Nothing to write in helper Script

#### Call a module

{% highlight php %}
require_once('helper.php');
{% endhighlight %}

-- -- --

### System Calls

Here we wrap <code>herbstclient</code> system call
in a function named <code>hc</code>.

<code class="code-file">helper.php</code>

{% highlight php %}
function hc($arguments) 
{
    system("herbstclient $arguments");
}
{% endhighlight %}

<code class="code-file">autostart.php</code>

{% highlight php %}
# Read the manual in $ man herbstluftwm
hc('emit_hook reload');

# gap counter
system("echo 35 > /tmp/herbstluftwm-gap");
{% endhighlight %}

-- -- --

### Array: Tag Names and Keys

<code class="code-file">config.php</code>

{% highlight php %}
$tag_names = range(1, 9);
$tag_keys  = array_merge(range(1, 9), [0]);
{% endhighlight %}

![HerbstluftWM: Tag Status][image-hlwm-02-tag-status]{: .img-responsive }

-- -- --

### Hash: Color Schemes

Using **key-value pairs**, a simple data structure.

<code class="code-file">gmc.php</code>

{% highlight php %}
$color = array(
    'white' => '#ffffff',
    'black' => '#000000',

    'grey50'  => '#fafafa',
    'grey100' => '#f5f5f5',
);
{% endhighlight %}

<code class="code-file">autostart.php</code>

{% highlight php %}
# background before wallpaper
system("xsetroot -solid '".COLOR['blue500']."'");
{% endhighlight %}

#### View Source File:

*	[gitlab.com/.../dotfiles/.../php/gmc.php][dotfiles-php-gmc]

{% include post/2017/05/herbstlustwm-modularized-gmc.md %}

-- -- --

### Hash: Config

The Hash in Config is very similar with the colors above.
Except that it has string interpolation all over the place.

<code class="code-file">config.php</code>

{% highlight php %}
# Modifier variables
$s = 'Shift';
$c = 'Control';
$m = 'Mod4';
$a = 'Mod1';

$keybinds = array(
  # session
    "$m-$s-q" => 'quit',
    "$m-$s-r" => 'reload',
    "$m-$s-c" => 'close'
)
{% endhighlight %}

This config will be utilized in main script
as shown in the following code.

<code class="code-file">autostart.php</code>

{% highlight php %}
do_config("keybind",   $keybinds);
do_config("keybind",   $tagskeybinds);
do_config("mousebind", $mousebinds);
do_config("attr",      $attributes);
do_config("set",       $sets);
do_config("rule",      $rules);
{% endhighlight %}

#### View Source File:

*	[gitlab.com/.../dotfiles/.../php/config.php][dotfiles-php-config]

{% include post/2017/05/herbstlustwm-modularized-config.md %}

-- -- --

### Processing The Hash Config

**This is the heart of this script**.
 
This <code>do-config</code> function has two arguments,
the herbstclient command i.e "keybind", and hash from config.
I can see how simple and clean, PHP is.

<code class="code-file">helper.php</code>

{% highlight php %}
function do_config($command, $hash) 
{
    # loop over hash
    foreach ($hash as $key => $value) {
        hc($command.' '.$key.' '.$value);

        # uncomment to debug in terminal
        # echo $command.' '.$key.' '.$value."\n";
    }
}
{% endhighlight %}

#### Debug Herbstclient Command

I do not remove line where I do debug when I made this script,
so anyone can use it later, avoid examining blindly.
Sometimes strange things happen.
Just uncomment this line to see what happened.

{% highlight php %}
        echo $command.' '.$key.' '.$value."\n";
{% endhighlight %}

You can see the debugging result in figure below.

[![HerbstluftWM: Debug Command][image-hlwm-03-debug-config]{: .img-responsive }][photo-hlwm-03-debug-config]

#### View Source File:

*	[gitlab.com/.../dotfiles/.../php/helper.php][dotfiles-php-helper]

{% include post/2017/05/herbstlustwm-modularized-helper.md %}

-- -- --

### Setting the Tags

Nothing special here,
PHP read all exported variable from modules,
but PHP function do not read the varable unless it defined global.

<code class="code-file">helper.php</code>

{% highlight php %}
function set_tags_with_name()
{
    global $tag_names, $tag_keys;
    hc("rename default '$tag_names[0]' 2>/dev/null || true");
    
    foreach($tag_names as $index=>$value) {
        hc("add '$value'");
        
        $key = $tag_keys[$index];
        if (!empty($key)) {
            hc("keybind Mod4-$key use_index '$index'");
            hc("keybind Mod4-Shift-$key move_index '$index'");
        }
    }
}
{% endhighlight %}

-- -- --

### Launch the Panel

Two more functions left, it is <code>do_panel</code>
and <code>startup_run</code>.

This two also be easy to do in PHP.
<code class="code-file">helper.php</code>

{% highlight php %}
function do_panel() 
{
    $panel   = __dir__."/panel-lemonbar.php";
    if (!is_executable($panel))
        $panel = "/etc/xdg/herbstluftwm/panel.sh";

    $raw = shell_exec('herbstclient list_monitors | cut -d: -f1');
    $monitors = explode("\n", trim($raw));

    foreach ($monitors as $monitor) {
        # start it on each monitor
        system("$panel $monitor > /dev/null &"); // no $output
    }
}
{% endhighlight %}

-- -- --

### Run Baby Run

This is the last part.
It is intended to be modified.
Everyone has their own personal preferences.

<code class="code-file">startup.php</code>

{% highlight php %}
function startup_run() 
{
    $command = 'silent new_attr bool my_not_first_autostart';
    system("herbstclient $command", $exitcode);

    # without redirection your app WILL HANG !
    if ( ! ((bool) trim($exitcode)) ) {
      # non windowed app
        system("compton > /dev/null &");
        system("dunst > /dev/null &");
        system("parcellite > /dev/null &");
        system("nitrogen --restore > /dev/null &");
        system("mpd > /dev/null &");

      # windowed app
        exec("xfce4-terminal > /dev/null 2>&1 &");
        exec("firefox > /dev/null 2>&1 &");
        exec("geany > /dev/null 2>&1 &");
        exec("thunar > /dev/null 2>&1 &");
    }
}
{% endhighlight %}

#### Specific PHP Issue

	Without redirection, your script will hang.

Be aware of the <code>> /dev/null &</code> part in PHP.
It is mandatory for running background processing.
The PHP manual has said it clearly.

#### View Source File:

*	[gitlab.com/.../dotfiles/.../php/startup.php][dotfiles-php-startup]

{% include post/2017/05/herbstlustwm-modularized-startup.md %}

-- -- --

### Putting It All Together.

The last part is going to main script
and putting it all back together.

	Now the flow is clear

<code class="code-file">Header Part: autostart.php</code>

{% highlight php %}
require_once('gmc.php');
require_once('helper.php');
require_once('config.php');
require_once('startup.php');
{% endhighlight %}

<code class="code-file">Procedural Part: autostart.php</code>

{% highlight php %}
# background before wallpaper
system("xsetroot -solid '".COLOR['blue500']."'");

# Read the manual in $ man herbstluftwm
hc('emit_hook reload');

# gap counter
system("echo 35 > /tmp/herbstluftwm-gap");

# do not repaint until unlock
hc("lock");

# standard
hc('keyunbind --all');
hc("mouseunbind --all");
hc("unrule -F");

set_tags_with_name();

# do hash config
do_config("keybind",   $keybinds);
do_config("keybind",   $tagskeybinds);
do_config("mousebind", $mousebinds);
do_config("attr",      $attributes);
do_config("set",       $sets);
do_config("rule",      $rules);

# unlock, just to be sure
hc("unlock");

# launch statusbar panel
do_panel();

# load on startup
startup_run();
{% endhighlight %}

#### View Source File:

*	[gitlab.com/.../dotfiles/.../php/autostart.php][dotfiles-php-autostart]

{% include post/2017/05/herbstlustwm-modularized-autostart.md %}

-- -- --

### Coming up Next

After the Window Manager, comes the Panel.

*	[HerbstluftWM Tag Status in PHP][local-php-tag-status]

*	[HerbstluftWM Event Idle in PHP][local-php-event-idle]

-- -- --

Happy Configuring.


[//]: <> ( -- -- -- links below -- -- -- )

{% assign asset_path = site.url | append: '/assets/posts/desktop/2017/05' %}
{% assign dotfiles_path = 'https://gitlab.com/epsi-rns/dotfiles/blob/master/herbstluftwm' %}

[local-php-tag-status]:   {{ site.url }}/desktop/2017/06/06/herbstlustwm-tag-status-php.html
[local-php-event-idle]:   {{ site.url }}/desktop/2017/06/16/herbstlustwm-event-idle-php.html

[image-ss-hlwm-nopanel]: {{ asset_path }}/herbstluftwm-nopanel.png
[photo-ss-hlwm-nopanel]: https://photos.google.com/share/AF1QipMO53TtSJVXrkn8R0s4wre4QWgX7_G5CoaSkFMneVHFp9Tu5STBmdjW3M3fpA2eEw/photo/AF1QipM5QN3sl9KsZs3xlb87UivcHZeLGbSuk2Z8Jx0W?key=WGIySDVOaVpibkJCRkV5NWVZUUs3UnNLNHR1MVpn

[image-hlwm-02-tag-status]:   {{ asset_path }}/hlwm-02-tag-status.png
[image-hlwm-03-debug-config]: {{ asset_path }}/hlwm-03-debug-config-half.png
[photo-hlwm-03-debug-config]: https://photos.google.com/share/AF1QipMO53TtSJVXrkn8R0s4wre4QWgX7_G5CoaSkFMneVHFp9Tu5STBmdjW3M3fpA2eEw/photo/AF1QipMG-pilS2yhwarThAT23bBYV54z3rYcs2wnaL0E?key=WGIySDVOaVpibkJCRkV5NWVZUUs3UnNLNHR1MVpn
[image-php-01-tree]:          {{ asset_path }}/hlwm-php-01-tree.png

[local-overview]: {{ site.url }}/desktop/2017/05/01/herbstlustwm-modularized-overview.html

[dotfiles-php-directory]: https://gitlab.com/epsi-rns/dotfiles/tree/master/herbstluftwm/php
[dotfiles-php-autostart]: {{ dotfiles_path }}/php/autostart.php
[dotfiles-php-gmc]:       {{ dotfiles_path }}/php/gmc.php
[dotfiles-php-config]:    {{ dotfiles_path }}/php/config.php
[dotfiles-php-helper]:    {{ dotfiles_path }}/php/helper.php
[dotfiles-php-startup]:   {{ dotfiles_path }}/php/startup.php
