Settings
========

Basic settings
--------------

PYBB_TOPIC_PAGE_SIZE
....................

Number of posts in topic page (default 10)

PYBB_FORUM_PAGE_SIZE
....................

Number of topics in forum page (default 10)

PYBB_FREEZE_FIRST_POST
......................

Freeze first post in topics (show on every page) (default False)

PYBB_DEFAULT_TITLE
..................

Default title for forum index page (default 'PYBB Powered Forum')

PYBB_DEFAULT_AUTOSUBSCRIBE
..........................

Users will be automatically subscribed to topic when create post in it. (default True)

PYBB_TOPIC_SUBSCRIBE_MODE
.........................

Available values are defined in pybb.defaults and are:

- ``SUBSCRIBE_MODE_ALL`` : every authenticated user can subscribe to a topic (default value)
- ``SUBSCRIBE_MODE_PERM`` : only users with ``pybb.subscribe_topic`` permission will be able to subscribe to a topic
- ``SUBSCRIBE_MODE_DISABLED`` : topic subscriptions are disabled


PYBB_DISABLE_NOTIFICATIONS
..........................

Users won't be able to subscribe to topics and alread subscribed users won't receive
notifications when a post is added / updated. Default to ``False``.

PYBB_USE_DJANGO_MAILER
......................

When True and django-mailer app installed, then for sending email pybbm will use this app. With django-mailer you can
manage emails from your site in queue. But in this case you have to periodically actually
send emails from queue. For more information see `app home page <https://github.com/pinax/django-mailer/>`_.
(default False)

PYBB_INITIAL_CUSTOM_USER_MIGRATION
..................................

Name of initial south migration in app where placed custom user model.
``None`` means that if app with custom user model has migrations, then '0001_initial.py' will be used by default
(default None)

Emoticons
---------

PYBB_SMILES_PREFIX
..................

Prefix for emoticons images set, related to STATIC_URL (default 'pybb/emoticons')

PYBB_SMILES
...........

Dict for emoticon replacement.
Key - text to be replaced, value - image name.

default::

    {
        '&gt;_&lt;': 'angry.png',
        ':.(': 'cry.png',
        'o_O': 'eyes.png',
        '[]_[]': 'geek.png',
        '8)': 'glasses.png',
        ':D': 'lol.png',
        ':(': 'sad.png',
        ':O': 'shok.png',
        '-_-': 'shy.png',
        ':)': 'smile.png',
        ':P': 'tongue.png',
        ';)': 'wink.png'
    }

e.g. text  ";)" in post will be replaced to::

    <img src="{{ STATIC_URL }}{{ PYBB_SMILES_PREFIX }}wink.png">

with default setting.

User profile settings
---------------------

Next settings used only if you don't customize user profile model,
user profile creation form or templates.

PYBB_PROFILE_RELATED_NAME
.........................

Related name from profile's OneToOne relationship to User model. If profile model is User
model itselt then set it to `None` (default 'pybb_profile')

For more information see :doc:`how to use custom user model with pybbm</customuser>`

PYBB_AVATAR_WIDTH and PYBB_AVATAR_HEIGHT
........................................

Avatar width and height respectively to use in templates (avatars scaled using sorl.thumbnail
if it installed and included in project) (default 80 and 80)

PYBB_MAX_AVATAR_SIZE
....................

Maximum avatar size, in bytes (default is 1024*50 wich is 50KB)

PYBB_DEFAULT_TIME_ZONE
......................

Default time zone for forum as integer. E.g. setting to 1 means GMT+1 zone. (default +3 Moscow)

PYBB_SIGNATURE_MAX_LENGTH
.........................

Limit of sybmols in user signature (default 1024)

PYBB_SIGNATURE_MAX_LINES
........................

Limit of lines in user signature (default 3)

PYBB_DEFAULT_AVATAR_URL
.......................

Will be used if user doesn't upload avatar (default settings.STATIC_URL + 'pybb/img/default_avatar.jpg')

Style
-----

You can use builtin templates with custom basic template.

PYBB_TEMPLATE
.............

Builtin templates will inherit this template (default "base.html")


Markup engines
--------------

PYBB_MARKUP
...........

Markup engine used in forum (default is 'bbcode' if you have a defined bbcode engine)
See PYBB_MARKUP_ENGINES below

PYBB_MARKUP_ENGINES_PATHS
.........................

Dict with avaiable markup engines path. One of them should be selected with PYBB_MARKUP

Markup engine should be a path to a class, that inherits from `pybb.markup.base.BaseParser`.
Markup engine should take care of replacing smiles in body with related emoticons.

by default PyBBM support `bbcode` and `markdown` markup::

    {
        'bbcode': 'pybb.markup.bbcode.BBCodeParser',
        'markdown': 'pybb.markup.markdown.MarkdownParser'
    }

Please note, that previous version of pybb used two different settings : 
`PYBB_MARKUP_ENGINES` and `PYBB_QUOTE_ENGINES` which were callables.
This is still supported, but is deprecated.

PYBB_MARKUP_ENGINES (deprecated)
................................

Should be the same dict with paths to markup engine classes as `PYBB_MARKUP_ENGINES_PATH` setting

default is `PYBB_MARKUP_ENGINES_PATHS`.

For more information see :doc:`markup`

PYBB_QUOTE_ENGINES (deprecated)
...............................

**Deprecation note: Every markup class must inherit from  `pybb.markup.base.BaseParser`**

**For more information see :doc:`markup`**

Should be the same dict with paths to markup engine classes as `PYBB_MARKUP_ENGINES_PATH` setting

default is `PYBB_MARKUP_ENGINES_PATHS`.

Post cleaning/validation
------------------------

PYBB_BODY_CLEANERS
..................

List of paths to 'cleaner' functions for body post to automatically remove undesirable content from posts.
Cleaners are user-aware, so you can disable them for some types of users.

Each function in list should accept `auth.User` instance as first argument and `string` instance as second, returned value will be sended to next function on list or saved and rendered as post body.

for example this is enabled by default `rstrip_str` cleaner::

    def rstrip_str(user, str):
        if user.is_staff:
            return str
        return '\n'.join([s.rstrip() for s in str.splitlines()])

default is::

    PYBB_BODY_CLEANERS = ['pybb.markup.base.rstrip_str', 'pybb.markup.base.filter_blanks']

PYBB_BODY_VALIDATOR
...................

Extra form validation for body of post.

Called as::

    PYBB_BODY_VALIDATOR(user, body)

at `clean_body` method of `PostForm` Here you can do various checks based on user stats.
E.g. allow moderators to post links and don't allow others. By raising::

    forms.ValidationError('Here Error Message')

You can show user what is going wrong during validation.

You can use it for example for time limit between posts, preventing URLs, etc.

default is None

Anonymous/guest posting
-----------------------

PYBB_ENABLE_ANONYMOUS_POST
..........................

Allow post for not-authenticated users. False by default.
See :doc:`anonymous posting</anonymous>` for details.

PYBB_ANONYMOUS_USERNAME
.......................

Username for anonymous posts. If no user with this username exists it will be created on first anonymous post.

PYBB_ANONYMOUS_VIEWS_CACHE_BUFFER
.................................

Number of anonymous views for each topic, that will be cached. For disabling caching anonymous views
just set it to `None`. 100 by default

Premoderation
-------------

PYBB_PREMODERATION
..................

Filter for messages that require pre-moderation. See :doc:`Pre-moderation</premoderation>` for details.

Attachments
-----------

PYBB_ATTACHMENT_ENABLE
......................

Enable attahcments for all users. `False` by default.

PYBB_ATTACHMENT_SIZE_LIMIT
..........................

Maximum attachment limit (in bytes), `1024*1024` (1MB) by default.

PYBB_ATTACHMENT_UPLOAD_TO
.........................

Directory in your media path for uploaded attacments. `pybb_upload/attachments` by default.

Polls
-----

Note: For disabling polls on your forum, write custom permission handler and return from `may_create_poll` method `False`
See `PYBB_PERMISSION_HANDLER` setting.

PYBB_POLL_MAX_ANSWERS
.....................

Max count of answers, that user can add to topic. 10 by default.

Permissions
-----------

PYBB_AUTO_USER_PERMISSIONS
..........................

Automatically adds add post and add topic permissions to users on user.save(). (default True)

PYBB_PERMISSION_HANDLER
.......................

If you need custom permissions (for example, private forums based on application-specific 
user groups), you can set `PYBB_PERMISSION_HANDLER` to a class which inherits from 
`pybb.permissions.DefaultPermissionHandler` (default), and override any of the `filter_*` and
`may_*` method. For details, look at the source of `pybb.permissions.DefaultPermissionHandler`.
All methods from permission handler (custom or default) can be used in templates as filters,
if loaded pybb_tags. In template will be loaded methods which start with 'may' or 'filter'
and with three or two arguments (include 'self' argument)
