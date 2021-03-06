Vinaigrette translates Django model data -- stored in the database -- using GNU gettext
and Django's standard internationalization features.

==========
Installing
==========

Add ``'vinaigrette'`` to INSTALLED_APPS in your settings.

Then, tell vinaigrette which fields you want to translate. In the appropriate ``models.py`` files::

    import vinaigrette
    vinaigrette.register(Ingredient, ['name', 'description'])
    
This tells vinaigrette to translate the ``name`` and ``description`` fields on Ingredient objects.

======
Using
======

After installing vinaigrette, the PO files generated by ``manage.py makemessages`` will include
strings from the registered fields. If a particular string is translated, the model value will
be the string translated into the appropriate language::

    >>> from django.utils.translation import activate
    >>> i = Ingredient(name=u'Lettuce')
    >>> i.name
    u'Lettuce'
    >>> activate('fr')
    >>> i.name
    u'Laitue'
    
Et cetera
=========

There are a couple of options to restrict which objects translation strings will be collected
from. See the docstring for ``vinaigrette.register``.

Vinaigrette adds a ``--keep-obsolete`` option to ``manage.py makemessages``, which prevents gettext
from deactivating translated messages no longer present in code or in registered database fields.

Vinaigrette is designed for database content that is:

- always edited in the default language
- edited by site administrators, not users

Only model instances are translated. Data accessed via the Django QuerySet ``values`` method will
not be translated.

In general, when a field is accessed, it will always return the translated version, if one exists.
However, if a value is set, the exact value entered (and not the translated version) should be saved
to the database. For example:

    >>> from django.utils.translation import activate
    >>> i = Ingredient(name=u'Lettuce')
    >>> activate('fr')
    >>> i.name
    u'Laitue'
    >>> i.name = 'Cabbage'
    >>> i.name
    u'Chou'
    >>> i.save()
    >>> Ingredient.objects.get(name='Cabbage').name
    u'Chou'

Help! The Admin is messing up all the vinaigrette fields whenever I save changes!
---------------------------------------------------------------------------------

Use `vinaigrette.VinaigrettteAdminLanguageMiddleware` to force the admin to
always use the main language, and not have vinaigrette mess with your
change views.

=======
WARNING
=======

This fork support only django 1.7.*. For more information take a look at
origin repo.
