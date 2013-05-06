---
layout: post
title: "Wordpress uppsetning með Heroku"
description: ""
category: 
tags: []
---
{% include JB/setup %}
**Uppsetning á WordPress á Heroku**

Ég skoðaði Jekyll um daginn til að hýsa bloggið mitt á GitHub. Ég var ekki nógu hrifinn af því. Ég ákvað að skoða Heroku í dag þess í stað og ákvað að setja upp WordPress þar.

Til þess að geta sett upp WordPress á Heroku verðurðu að setja upp aðgang þar.

Einnig þarf að setja upp “heroku toolbelt”. Hvað gerir heroku toolbelt, það setur upp eftirfarandi þrjá hluti:

	- Heroku client – CLI tool for creating and managing Heroku apps
	- Foreman – an easy option for running your apps locally
	- Git – revision control and pushing to Heroku

**GETTING STARTED**

Once installed, you’ll have access to the heroku command from your command shell. Log in using the email address and password you used when creating your Heroku account:

```
$ heroku login
Enter your Heroku credentials.
Email: adam@example.com
Password:
Could not find an existing public key.
Would you like to generate one? [Yn]
Generating new SSH public key.
Uploading ssh public key /Users/adam/.ssh/id_rsa.pub
You’re now ready to create your first Heroku app:
```

```
$ cd ~/myapp
$ heroku create
Creating stark-fog-398... done, stack is cedar
http://stark-fog-398.herokuapp.com/ | git@heroku.com:stark-fog-398.git
Git remote heroku added
```

Þegar settur hefur verið upp Heroku aðgangur og Heroku toolbelt þá er hægt að snúa sér að því að setja upp WordPress. Afritið textann og fylgið leiðbeiningunum og það ætti að vera uppsett WordPress síða eftir 10 mínútur.

Ég afritaði beint af GitHub síðunni sem er til fyrir prójektið. Það er tengill neðst á GitHub síðuna.

```
git clone git://github.com/mhoofman/wordpress-heroku.git
```

With the Heroku gem, create your app

```
$ cd wordpress-heroku
$ heroku create
> Creating strange-turtle-1234... done, stack is cedar
> http://strange-turtle-1234.herokuapp.com/ | git@heroku.com:strange-turtle-1234.git
> Git remote heroku added
Promote the database (replace COLOR with the color name from the above output)

$ heroku pg:promote HEROKU_POSTGRESQL_COLOR
> Promoting HEROKU_POSTGRESQL_COLOR to DATABASE_URL... done
Create a new branch for any configuration/setup changes needed

$ git checkout -b production
Copy the wp-config.php

$ cp wp-config-sample.php wp-config.php
Update unique keys and salts in wp-config.php on lines 48-55. WordPress can provide random values here.

define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');
Clear .gitignore and commit wp-config.php

$ >.gitignore
$ git add .
$ git commit -m "Initial WordPress commit"
Deploy to Heroku

$ git push heroku production:master
> -----> Heroku receiving push
> -----> PHP app detected
> -----> Bundling Apache v2.2.22
> -----> Bundling PHP v5.3.10
> -----> Discovering process types
>        Procfile declares types -> (none)
>        Default types for PHP   -> web
> -----> Compiled slug size is 13.8MB
> -----> Launcing... done, v5
>        http://strange-turtle-1234.herokuapp.com deployed to Heroku
>
> To git@heroku:strange-turtle-1234.git
> * [new branch]    production -> master
```

https://github.com/mhoofman/wordpress-heroku-
