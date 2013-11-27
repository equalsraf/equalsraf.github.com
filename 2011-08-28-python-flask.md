title: Python awesomeness in a Flask
icon: http://flask.pocoo.org/docs/_static/flask.png
date: 2011-08-28
description: Flask a python web microframework
nocomments:

While I was working on updating blogpit, I discovered this wonderful web microframework called Flask. 
Naturally it is python and of course it is awesome.
I was immediately attracted to Flask for two reasons: First it has a lot in common with Django, and second it strives to be simple.

So here is a quick overview of Flask, from the eyes of a Django user.

## The pros
- Routing URLs is EASY! Nice use of python decorators
- It shares a lot of common ground with Django

### Routing

Mapping URL paths to methods with function decorators is just brilliant, and easy too.
Just add the decorator with the path expression on top of the view function.

    from flask import Flask
    app = Flask(__name__)
    
    @app.route('/hello')
    def hello():
        return 'Hello world!'
    
    app.run(host='0.0.0.0')

The expression can also hold placeholders that are mapped into function arguments, with integers, floats or paths.
Here is a more complex declaration for a view that takes a path as argument and accepts both POST and GET requests.

    @app.route('/<path:path>', methods=['GET', 'POST'])
    def hello():
        return 'Hello world! @ %s' % path


### Flask and Django

Flask is not a full replacement for Django, being a "microframework", it lacks a lot of the advanced features.
But that is fine for small projects.
They both share the same template language (Jinja), with different tags.
And Flask also makes it easy to use memcache, or a local cache for development.
Besides that the usuall requirements are all in place, support FastCGI, UWSGI.
Great documentation and some usefull code snippets are also a big help.

## The cons

Now for the missing features, and aspects I found more frustrating.
None of these are particularly deal breaking, but some of them required some extra work.

- In-template URL construction is not as good as I would like
- No database/ORM
- No forms
- Static files
- No syndication

### Building URLs

Building URLs in templates is slightly painful.
Django also took a while until it got this right.
For example to point at static content,


    <script src="{{url_for('static', filename='js/fancybox/jquery.fancybox-1.3.2.js')}}" type="text/javascript"></script>

A little more convoluted example involves the initial dot(.) that points to relative views, which is great for re-usability, but I keep mistyping the dot or the apostrophes.
In this regard I do prefer the Django syntax to pass arguments to tags.

    <li><a href="{{ url_for('.blogpit_content', path=section) }}">{{ section }}</a></li>

More importantly there is no trivial way to replace the 'static' URLs used for development with the remote ones.

### The missing bits
Flask has no forms and database/ORM.
For my website, I really don't need ORM, since I store persistent data in Git.
Forms however are something I can't go without.
Fortunately there are several Flask extensions that can be used for forms (Flask-WTF), for database storage you can also use any python solution of your choice (SqlAlchemy for example).

### Static files

My biggest frustration with Flask so far was the _static_ content view.
The static view is mostly equivalent to the Django's MEDIA_URL static file url.
While I do prefer Flask's approach of defining a proper view for static content, it frustrates me that there is no trivial way to replace the content location with another URL (say media.ruiabreu.org).
This is actually essential for easy deployment, so I ended up creating a template tag for this.

    # Handle static content
    def static(path):
        root = app.config.get('STATIC_ROOT', None)
        if root is None:
            return url_for('static', filename=path)
        if not root.endswith('/'):
            root += '/'
        return urlparse.urljoin(root, path)

    @app.context_processor
    def inject_static():
        return dict(static=static)

So instead of

    <script src="{{url_for('static', filename='js/fancybox/jquery.fancybox-1.3.2.js')}}" type="text/javascript"></script>

I use a custom template tag called _static_, that changes the static content URL based on a setting STATIC_ROOT

     <script src="{{ static('js/fancybox/jquery.fancybox-1.3.2.js')}}" type="text/javascript"></script>


### No Syndication

Another feature I realized was missing, was syndication support (RSS, Atom, etc).
While Flask has some support for Atom (but not RSS) it is just not up to par with what I'm used to in Django.
As a result I ended up cooking an RSS Jinja2 template to generate a feed.
This is far from optimal, but works well enough™.



## Test-drive
I liked Flask so much, that as a challenge I decided to re-implement my website in Flask.
Obviously I cheated, I got to reuse the static content and even some of the Jinja templates.
Also I left some of the _hard_ features out: OpenID, and my custom rate limiting for POST requests.

After 2 hours I had a site that, minus the login button, could very well pass for my Django site.
Granted that the whole code structure was not something to be proud of, and reusing it in anything else would be hell - but it did work - which is a lot to be said for the first time someone works with a web framework.



## The veredict

The general result, I'm very pleased with Flask.

Since I started writing this post, I actually rewrote the entire website in Flask, and I'm looking forward to migrate from Django.
Make no mistake, I still love Django, but I can't pass the opportunity of making things simpler.
Flask is just that, a simpler framework for simpler applications, and that suits me just fine.
The code for using flask with blogpit is now available, as Flask-blogpit, check the link to my repository at the end.

## References
- [Flask](http://flask.pocoo.org/)
- [Flask Extensions](http://flask.pocoo.org/extensions/)
- [Flask-blogpit](https://bitbucket.org/equalsraf/flask-blogpit)



