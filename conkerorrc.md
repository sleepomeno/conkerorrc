Using Emcas org-mode babel facilities you can tangle the source blocks
and create a javascript file. Default key binding for that: C-c C-v t

# Conkeror Configuration

## Set Paths

### Themes

The directory where your theme directories are located

    var themes = "~/.conkerorrc/theme";

## Adjust Load Paths

    load_paths.unshift("chrome://conkeror-contrib/content/");
    theme_load_paths.unshift(themes);

## General

    // Don't open a new window when another application requests a page
    url_remoting_fn = load_url_in_new_buffer;
    
    // History is wiped out every 7 days
    session_pref('browser.history_expire_days', 7);

### Enable sessions

    require("session.js");
    
    session_auto_save_auto_load = true;

### External Editor

Use emacs as your external editor (for watching the source e.g.)

    editor_shell_command = "emacsclient";
    view_source_use_external_editor = true;

### User agent

Some pages only show crap given the "default" user agent.

    require("user-agent-policy");
    
    user_agent_policy.define_policy("default",
                                    user_agent_firefox(),
                                    "images.google.com",
                                    build_url_regexp($domain = /(.*\.)?google/, $path = /images|search\?tbm=isch/),
                                    "plus.google.com");
    
    user_agent_policy.define_policy("firefoxcompatmode",
                                    "Mozilla/5.0 (X11; Linux x86_64; rv:35.0) Gecko/20100101 Firefox/35.0 conkeror/1.0pre1",
                                    "de.eurosport.yahoo.com");

## Theme

    theme_unload("default");
    theme_load("conkeror-theme-zenburn");

## Minibuffer

### Auto completion in minibuffer

    url_completion_use_history = true;
    url_completion_use_bookmarks = true;
    url_completion_use_webjumps = true;
    minibuffer_auto_complete_default = true;

### Valid urls

Conkeror must decide whether you enter a URL in the minibuffer or
refer to a webjump or bookmark. **possibly<sub>valid</sub><sub>url</sub>** defines a pattern
for deciding whether the input is an URL.

    function possibly_valid_url (str) {
        return /^\s*[^\/\s]*(\/|\s*$)/.test(str)
            && /[:\/\.]/.test(str);
    }

## Mode-line

Configuring the Conkeror's status line

    require("mode-line.js");
    
    add_hook("mode_line_hook", mode_line_adder(buffer_icon_widget), true);
    add_hook("mode_line_hook", mode_line_adder(loading_count_widget), true);
    add_hook("mode_line_hook", mode_line_adder(buffer_count_widget), true);
    add_hook("mode_line_hook", mode_line_adder(zoom_widget));
    add_hook("mode_line_hook", mode_line_adder(downloads_status_widget));
    
    // Remove the clock
    remove_hook("mode_line_hook", mode_line_adder(clock_widget));
    
    require("favicon.js");
    add_hook("mode_line_hook", mode_line_adder(buffer_icon_widget), true);
    read_buffer_show_icons = true;

## Hints

    hints_display_url_panel = true;
    hints_minibuffer_annotation_mode(true);
    
    hints_auto_exit_delay = 200

## Download configuration

    download_buffer_automatic_open_target=OPEN_NEW_BUFFER_BACKGROUND;

### Download directory

Set **cwd** to your Download directory

    // cwd = get_home_directory();
    // cwd.append("/Downloads/");

## Content handlers

Content handlers specify with what program downloaded files should be opened

### PDF

    content_handlers.set("application/pdf", content_handler_open_default_viewer);
    external_content_handlers.set("application/pdf", "evince");

### Torrent

    content_handlers.set("application/x-bittorrent", content_handler_open);
    external_content_handlers.set("application/x-bittorrent", "transmission-gtk");

### Libreoffice

I use libreoffice for everything office-like:

    external_content_handlers.set(
        "application/vnd.ms-excel",
        "libreoffice"
    );
    external_content_handlers.set(
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
        "libreoffice"
    );
    external_content_handlers.set(
        "application/vnd.openxmlformats-officedocument.presentationml.presentation",
        "libreoffice"
    );
    external_content_handlers.set(
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
        "libreoffice"
    );

## Addons configuration

These are the typical settings to enable using add-ons in Conkeror

    session_pref('extensions.checkCompatibility', false);
    session_pref("xpinstall.whitelist.required", false);
    user_pref("extensions.checkUpdateSecurity", true);

## Firebug-Lite

Firebug does not work in Conkeror but Firebug-Lite does

    define_variable("firebug_url", "http://getfirebug.com/releases/lite/1.2/firebug-lite-compressed.js");
    
    function firebug (I) {
        var doc = I.buffer.document;
        var script = doc.createElement('script');
        script.setAttribute('type', 'text/javascript');
        script.setAttribute('src', firebug_url);
        script.setAttribute('onload', 'firebug.init();');
        doc.body.appendChild(script);
    }
    interactive("firebug", "open firebug lite", firebug);

## Extensions

    require("adblockplus"); // Need to install the xpi!
    require('eye-guide.js'); // Smoother scrolling
    
    require("reddit");
    require("gmail");
    require("feedly");
    require("twitter");
    
    page_mode_deactivate(stackexchange_mode);
    page_mode_deactivate(youtube_mode)
    page_mode_deactivate(youtube_player_mode);

## Tabs

Shows buffers as tabs

    require("new-tabs.js");
    //Open Middle-Clicked Links in New Buffers
    require("clicks-in-new-buffer.js");
    clicks_in_new_buffer_target = OPEN_NEW_BUFFER_BACKGROUND;
    // Now buffers open in background. //Bind Number Keys to Switch to Buffers 1-10
    function define_switch_buffer_key (key, buf_num) { define_key(default_global_keymap, key, function (I) { switch_to_buffer(I.window, I.window.buffers.get_buffer(buf_num)); }); } for (let i = 0; i < 10; ++i) { define_switch_buffer_key(String((i+1)%10), i); }
    tab_bar_show_icon = true;

## Keybindings

### Links

    define_key(content_buffer_normal_keymap, "F", "follow-new-buffer-background"); 
    define_key(content_buffer_normal_keymap, "G", "find-url-new-buffer");

### Kill Buffer

    define_key(default_global_keymap, "x", "kill-current-buffer");
    define_key(content_buffer_normal_keymap, "x", "kill-current-buffer");
    define_key(content_buffer_normal_keymap, "C-w", "kill-current-buffer");
    define_key(default_global_keymap, "C-w", "kill-current-buffer");

### Reloading

    // Reload buffer
    define_key(content_buffer_normal_keymap, "f5", "reload");
    
    // Reload configuration
    define_key(default_global_keymap, "R", "reinit");
    define_key(content_buffer_normal_keymap, "R", "reinit");

### Buffer movement

    define_key(default_global_keymap, "M-J", "buffer-move-backward");
    define_key(default_global_keymap, "M-K", "buffer-move-forward");
    define_key(default_global_keymap, "J", "buffer-previous");
    define_key(default_global_keymap, "C-S-tab", "buffer-previous");
    define_key(default_global_keymap, "K", "buffer-next");
    define_key(default_global_keymap, "C-tab", "buffer-next");

### Scrolling

    define_key(content_buffer_normal_keymap, "D", "cmd_scrollBottom");
    define_key(content_buffer_normal_keymap, "U", "scroll-top-left");
    define_key(content_buffer_normal_keymap, "h", "scroll-beginning-of-line");
    define_key(content_buffer_normal_keymap, "l", "scroll-end-of-line");

### Movement

    define_key(content_buffer_normal_keymap, "back_space", "eye-guide-scroll-up");
    define_key(content_buffer_normal_keymap, "d", "eye-guide-scroll-down");
    define_key(content_buffer_normal_keymap, "C-d", "eye-guide-scroll-down");
    define_key(content_buffer_normal_keymap, "u", "eye-guide-scroll-up");
    
    define_key(content_buffer_normal_keymap, "L", "forward")
    define_key(content_buffer_normal_keymap, "H", "back")
    define_key(content_buffer_normal_keymap, "j", "cmd_scrollLineDown")
    define_key(content_buffer_normal_keymap, "k", "cmd_scrollLineUp")

### Alternate Url

    define_key(content_buffer_normal_keymap, "C-l", "find-alternate-url");

### Show help page

I'm used to open a new empty tab with C-t, so this is a hack to get a
similar behavior

    define_key(content_buffer_normal_keymap, "C-t", "help-page");

### Execute

    define_key(default_global_keymap, "space", "execute-extended-command");
    define_key(content_buffer_normal_keymap, "space", "execute-extended-command");

### Org-protocol

    define_key(content_buffer_normal_keymap, "C-c c l", "org-capture-link");
    define_key(content_buffer_normal_keymap, "C-c c u", "org-capture-lookat");
    define_key(content_buffer_normal_keymap, "C-c c t", "org-capture-todo");

### Selecting

    define_key(content_buffer_normal_keymap, "M-h", "cmd_selectCharPrevious");
    define_key(content_buffer_normal_keymap, "M-l", "cmd_selectCharNext");
    define_key(content_buffer_normal_keymap, "M-k", "cmd_selectLinePrevious");
    define_key(content_buffer_normal_keymap, "M-j", "cmd_selectLineNext");

1.  Copy and Paste

        define_key(content_buffer_normal_keymap, "C-c p", "cmd_copy");
        define_key(text_keymap, "C-c", "cmd_copy");
        define_key(default_global_keymap, "C-y", "paste-url");
        define_key(text_keymap, "C-v", "paste-x-primary-selection");

2.  Copy Url

        interactive("copy-url",
                    "Copy the current buffer's URL to the clipboard",
                    function(I) {
                        var text = I.window.buffers.current.document.location.href;
                        writeToClipboard(text);
                        I.window.minibuffer.message("copied: " + text);
                    }
                   );
        define_key(default_global_keymap, "C-c u", "copy-url");

### Esc alias

There is a need for an ESC alias, see <http://truongtx.me/2013/08/08/using-esc-key-in-conkeror/>

    require("global-overlay-keymap");
    define_key_alias("C-o", "escape");

## Org-Protocol

    // org-protocol stuff
    function org_capture (url, title, selection, window, cmd_str) {
        if (window != null) {
            window.minibuffer.message('Issuing ' + cmd_str);
        }
        shell_command_blind(cmd_str);
    
    }
    function org_capture_link (url, title, selection, window) {
        var cmd_str = 'emacsclient \"org-protocol:/capture:/i/'+url+'/'+title+'/\"';
        org_capture(url,title,selection,window,cmd_str);
    }
    function org_capture_lookat (url, title, selection, window) {
        var cmd_str = 'emacsclient \"org-protocol:/capture:/u/'+url+'/'+title+'/\"';
        org_capture(url,title,selection,window,cmd_str);
    }
    function org_capture_todo (url, title, selection, window) {
        var cmd_str = 'emacsclient \"org-protocol:/capture:/t/'+url+'/'+title+'/\"';
        org_capture(url,title,selection,window,cmd_str);
    }
    
    interactive("org-capture-link", "Bookmark",
                function (I) {
                    org_capture_link(encodeURIComponent(I.buffer.display_uri_string),
                                     encodeURIComponent(I.buffer.document.title),
                                     encodeURIComponent(I.buffer.top_frame.getSelection()),
                                     I.window);
                });
    interactive("org-capture-lookat", "Lookat",
                function (I) {
                    org_capture_lookat(encodeURIComponent(I.buffer.display_uri_string),
                                       encodeURIComponent(I.buffer.document.title),
                                       encodeURIComponent(I.buffer.top_frame.getSelection()),
                                       I.window);
                });
    interactive("org-capture-todo", "Todo",
                function (I) {
                    org_capture_todo(encodeURIComponent(I.buffer.display_uri_string),
                                     encodeURIComponent(I.buffer.document.title),
                                     encodeURIComponent(I.buffer.top_frame.getSelection()),
                                     I.window);
                });

## Enable Scrollbars

    function enable_scrollbars (buffer) {
        buffer.top_frame.scrollbars.visible = true;
    }
    add_hook("create_buffer_late_hook", enable_scrollbars);

## Webjumps

    // Make the *google* webjump the default action
    read_url_handler_list = [read_url_make_default_webjump_handler("google")];
    
    define_webjump("y",   "http://www.youtube.com/results?search_query=%s&aq=f", $alternative = "http://www.youtube.com");
    define_webjump("lp-home", "chrome://lastpass/content/home2.xul");
    define_webjump("red", "http://www.reddit.com/r/%s");
    define_webjump("fac", "http://www.facebook.com");
    define_webjump("or", "http://www.orf.at");
    define_webjump("eu", "http://de.eurosport.yahoo.com");
    define_webjump("ho", "https://www.haskell.org/hoogle/?hoogle=%s");
    define_webjump("hag", "http://google.com/search?as_sitesearch=hackage.haskell.org%2Fpackage&as_q=%s");
    define_webjump("hac", "http://hackage.haskell.org/package/%s");
    webjumps.g = webjumps.google;
    
    define_webjump("amazon", "http://www.amazon.de/s/ref=nb_sb_noss?field-keywords=%s", $alternative = "http://www.amazon.de");
    define_webjump(">conkeror", "http://conkeror.org/?action=fullsearch&context=60&value=%s&fullsearch=Text");
    delete webjumps.conkerorwiki;
    define_webjump("twit", "https://tweetdeck.twitter.com");
    define_webjump("gmail", "https://mail.google.com/mail/u/0/#inbox"); 
    define_webjump("le", "http://dict.leo.org/#/search=%s&searchLoc=0&resultOrder=basic&multiwordShowSingle=on");
    define_webjump("lf", "http://dict.leo.org/frde/index_de.html#/search=%s&searchLoc=0&resultOrder=basic&multiwordShowSingle=on");
    define_webjump("st","http://stackoverflow.com/search?q=%s", $alternative="http://stackoverflow.com");
    
    define_webjump("hay", "http://hayoo.fh-wedel.de/?query=%s");
    
    define_webjump("topocket", "javascript:(function(){var e=function(t,n,r,i,s){var o=[4651073,5020134,2761518,1664123,6345041,1843517,4685030,4901553,4592258,4422327];var i=i||0,u=0,n=n||[],r=r||0,s=s||0;var a={'a':97,'b':98,'c':99,'d':100,'e':101,'f':102,'g':103,'h':104,'i':105,'j':106,'k':107,'l':108,'m':109,'n':110,'o':111,'p':112,'q':113,'r':114,'s':115,'t':116,'u':117,'v':118,'w':119,'x':120,'y':121,'z':122,'A':65,'B':66,'C':67,'D':68,'E':69,'F':70,'G':71,'H':72,'I':73,'J':74,'K':75,'L':76,'M':77,'N':78,'O':79,'P':80,'Q':81,'R':82,'S':83,'T':84,'U':85,'V':86,'W':87,'X':88,'Y':89,'Z':90,'0':48,'1':49,'2':50,'3':51,'4':52,'5':53,'6':54,'7':55,'8':56,'9':57,'\/':47,':':58,'?':63,'=':61,'-':45,'_':95,'&':38,'$':36,'!':33,'.':46};if(!s||s==0){t=o[0]+t}for(var f=0;f<t.length;f++){var l=function(e,t){return a[e[t]]?a[e[t]]:e.charCodeAt(t)}(t,f);if(!l*1)l=3;var c=l*(o[i]+l*o[u%o.length]);n[r]=(n[r]?n[r]+c:c)+s+u;var p=c%(50*1);if(n[p]){var d=n[r];n[r]=n[p];n[p]=d}u+=c;r=r==50?0:r+1;i=i==o.length-1?0:i+1}if(s==260){var v='';for(var f=0;f<n.length;f++){v+=String.fromCharCode(n[f]%(25*1)+97)}o=function(){};return v+'6d3894d6d5'}else{return e(u+'',n,r,i,s+1)}};var t=document,n=t.location.href,r=t.title;var i=e(n);var s=t.createElement('script');s.type='text/javascript';s.src='https://getpocket.com/b/r4.js?h='+i+'&u='+encodeURIComponent(n)+'&t='+encodeURIComponent(r);e=i=function(){};var o=t.getElementsByTagName('head')[0]||t.documentElement;o.appendChild(s)})()");
    
    define_webjump("pock","https://getpocket.com/a/queue/");
    
    define_webjump("cal","https://www.google.com/calendar/render");

### Lastpass

You need to grab these bookmarklets from your Lastpass web page!

    define_webjump("login-lastpass", "javascript:((function(){/*AutoLogin_LastPass*/_LPG=function(i){return%20document.getElementById(i);};_LPT=function(i){return%20document.getElementsByTagName(i);};if(_LPG('_lpiframe')){_LPG('_lpiframe').parentNode.removeChild(_LPG('_lpiframe'));}if(_LPG('_LP_RANDIFRAME')){_LPG('_LP_RANDIFRAME').parentNode.removeChild(_LPG('_LP_RANDIFRAME'));}_LASTPASS_INC=function(u,s){if(u.match(/_LASTPASS_RAND/)){alert('Cancelling_request_may_contain_randkey');return;}s=document.createElement('script');s.setAttribute('type','text/javascript');s.setAttribute('src',u);if(typeof(window.attachEvent)!='undefined'){if(_LPT('body').length){_LPT('body').item(0).appendChild(s);}else{_LPT('head').item(0).appendChild(s);}}else{if(_LPT('head').length){_LPT('head').item(0).appendChild(s);}else{_LPT('body').item(0).appendChild(s);}}};_LASTPASS_INC('https://lastpass.com/bml.php'+String.fromCharCode(63)+'v=0&a=1&r='+Math.random()+'&h=b5425ca045f33dacd72a84fa2c9bd0ace47eeb15c5479e26e51bd9f959875646&u='+escape(document.location.href));_LPM=function(m){var%20targetFrame=_LPG(m.data.frame);if(null!=targetFrame&&typeof(targetFrame)!='undefined'&&typeof(targetFrame.contentWindow)!='undefined')targetFrame.contentWindow.postMessage(m.data,'*');};if(window.addEventListener){window.addEventListener('message',_LPM,false);}else{window.attachEvent('onmessage',_LPM);}var%20t=document.createElement('iframe');t.setAttribute('id','_LP_RANDIFRAME');t.setAttribute('sandbox','allow-scripts');t.frameBorder='0';t.setAttribute('src','https://lastpass.com/bml.php?u=1&hash=1&gettoken=0&donotcache=14215094111802924761');t.setAttribute('onload',\"document.getElementById('_LP_RANDIFRAME').contentWindow.postMessage('86f644a1848bde3c8e7ff34271149dc759f9318c182060b00abd82b8c923942f','*');\");if(typeof(window.attachEvent)!='undefined'){if(_LPT('body').length){_LPT('body').item(0).appendChild(t);}else{document.getElementByTagName('head').item(0).appendChild(t);}}else{if(_LPT('head').length){_LPT('head').item(0).appendChild(t);}else{_LPT('body').item(0).appendChild(t);}}})());");
    define_webjump("fill-lastpass" , "javascript:((function(){/*AutoFill_LastPass*/_LPG=function(i){return%20document.getElementById(i);};_LPT=function(i){return%20document.getElementsByTagName(i);};if(_LPG('_lpiframe')){_LPG('_lpiframe').parentNode.removeChild(_LPG('_lpiframe'));}if(_LPG('_LP_RANDIFRAME')){_LPG('_LP_RANDIFRAME').parentNode.removeChild(_LPG('_LP_RANDIFRAME'));}_LASTPASS_INC=function(u,s){if(u.match(/_LASTPASS_RAND/)){alert('Cancelling_request_may_contain_randkey');return;}s=document.createElement('script');s.setAttribute('type','text/javascript');s.setAttribute('src',u);if(typeof(window.attachEvent)!='undefined'){if(_LPT('body').length){_LPT('body').item(0).appendChild(s);}else{_LPT('head').item(0).appendChild(s);}}else{if(_LPT('head').length){_LPT('head').item(0).appendChild(s);}else{_LPT('body').item(0).appendChild(s);}}};_LASTPASS_INC('https://lastpass.com/bml.php'+String.fromCharCode(63)+'v=0&a=0&r='+Math.random()+'&h=b5425ca045f33dacd72a84fa2c9bd0ace47eeb15c5479e26e51bd9f959875646&u='+escape(document.location.href));_LPM=function(m){var%20targetFrame=_LPG(m.data.frame);if(null!=targetFrame&&typeof(targetFrame)!='undefined'&&typeof(targetFrame.contentWindow)!='undefined')targetFrame.contentWindow.postMessage(m.data,'*');};if(window.addEventListener){window.addEventListener('message',_LPM,false);}else{window.attachEvent('onmessage',_LPM);}var%20t=document.createElement('iframe');t.setAttribute('id','_LP_RANDIFRAME');t.setAttribute('sandbox','allow-scripts');t.frameBorder='0';t.setAttribute('src','https://lastpass.com/bml.php?u=1&hash=1&gettoken=0&donotcache=14215114741929651210');t.setAttribute('onload',\"document.getElementById('_LP_RANDIFRAME').contentWindow.postMessage('86f644a1848bde3c8e7ff34271149dc759f9318c182060b00abd82b8c923942f','*');\");if(typeof(window.attachEvent)!='undefined'){if(_LPT('body').length){_LPT('body').item(0).appendChild(t);}else{document.getElementByTagName('head').item(0).appendChild(t);}}else{if(_LPT('head').length){_LPT('head').item(0).appendChild(t);}else{_LPT('body').item(0).appendChild(t);}}})());");

## Clear history

    function history_clear () {
        var history = Cc["@mozilla.org/browser/nav-history-service;1"]
                .getService(Ci.nsIBrowserHistory);
        history.removeAllPages();
    }
    interactive("history-clear",
                "Clear the history.",
                history_clear);

## Sqlite Manager

Needs the sqlitemanager xpi installed!

    interactive("sqlite-manager",
        "Open SQLite Manager window.",
        function (I) {
            make_chrome_window('chrome://SQLiteManager/content/sqlitemanager.xul');
        });

## Facebook extended mode

I use the extended mode from <http://truongtx.me/conkeror-extended-facebook-mode.html>

    let (path = get_home_directory()) {
      // add to load path
      path.appendRelativePath(".conkerorrc");
      path.appendRelativePath("facebook");
      load_paths.unshift(make_uri(path).spec);
    
      // include the library
      require("conkeror-extended-facebook-mode.js");  
    };

### Keybindings

    define_key(facebook_keymap, "1", "cefm-open-home");
    define_key(facebook_keymap, "2", "cefm-open-messages");
    define_key(facebook_keymap, "3", "cefm-open-notification");
    define_key(facebook_keymap, "C-M-o", "cefm-open-current-story-new-buffer");
    define_key(facebook_keymap, "C-O", "cefm-open-current-story-new-buffer-background");
    define_key(facebook_keymap, "C-M-m", "cefm-expand-content");
    
    // Chats
    cefm_scroll_gap = 50;
    define_key(facebook_keymap, "C-i", "cefm-cycle-conversations");
    define_key(facebook_keymap, "C-j", "cefm-scroll-up-current-coversation");
    define_key(facebook_keymap, "C-k", "cefm-scroll-down-current-coversation");
