# Tutorial

## Overview

This page gives an overview of the Gettext Commons and
describes how to integrate the library into existing Java
applications.

### Quick Links 

[I18nExample](../src/examples/I18nExample.java)

[Documentation](../javadoc/gettext-commons/apidocs/index.html)

## Table of Contents

* Requirements
* Installation
* Internationalization
* Creating Resource Bundles
* Loading Resource Bundles
* Maven Integration 


## Requirements

* Java 1.3 (or higher) 
* GNU gettext 



## Installation

### Windows

### _nix e MacOS

Most Unix systems and Linux distributions ship with gettext packages


#### Internationalization

The Gettext Commons provide the class 
[](http://xnap-commons.sourceforge.net/gettext-commons/apidocs/org/xnap/commons/i18n/I18n.html)


that has methods that need to be invoked each time a user visible
string is used: 
      
```java
I18n i18n = I18nFactory.getI18n(getClass());
System.out.println(i18n.tr("This text will be translated"));
```

I18n also supports proper handling of plurals:

```java
System.out.println(i18n.trn("Copied file.", "Copied files.", 1));
// will print "Copied file."

System.out.println(i18n.trn("Copied file.", "Copied files.", 4));
// will print "Copied files."
```

In addition there are convenience methods that use the Java API
MessageFormat.format() for substitution:

```java
System.out.println(i18n.tr("Folder: {0}", new File("/home/xnap-commons"));
System.out.println(i18n.trn("{0} file", "{0} files", 5, new Integer(5)));
```

And sometimes it is necessary to provide different
translations of the same word as some words may have multiple
meanings in the native language the program is written but
not in other languages:

```java
System.out.println(i18n.trc("chat (verb)", "chat"));
System.out.println(i18n.trc("chat (noun)", "chat"));
```
The preferable way to create an I18n object is through the [I18nFactory](http://xnap-commons.sourceforge.net/gettext-commons/apidocs/org/xnap/commons/i18n/I18nFactory.html)

The factory caches the I18n object internally using the the package
name of the provided class object and registers it with
I18nManager. Thus all classes of the same package will use the same
I18n instance.
      

```java
public class SampleClass
{
	private static I18n i18n = I18nFactory.getI18n(SampleClass.class);

	String localizedString;

	public SampleClass()
	{
	        localizedString = i18n.tr("Hello, World");
	}
}
```

[I18nManager](http://xnap-commons.sourceforge.net/gettext-commons/apidocs/org/xnap/commons/i18n/I18nManager.html)

lets you register independently created I18n objects and provides
the facility to change the locale of all registered I18n objects thereby
notifying possible LocaleChangeListeners:

```java
public class LocaleChangeAwareClass implements LocaleChangeListener
{
	private static I18n i18n = I18nFactory.getI18n(LocaleChangeAwareClass.class);

	String localizedString;

	public LocaleChangeAwareClass()
	{
	        localizedString = i18n.tr("Hello, World");
	        I18nManager.getInstance().addWeakLocaleChangeListener(this);
	}

	public void localeChanged(LocaleChangeEvent event)
	{
	        // update strings
	        localizedString = i18n.tr("Hello, World");
	        ...
	}
}
```

## Creating Resource Bundles

Once the source code has been internationalized, i.e. all
user visible strings are wrapped by a call to
<code>i18n.tr()</code>, xgettext can be used to extract these
strings for localization. 

<p>This 3 step process is illustrated in this figure:</p>

![gettext structure](gettext-structure.png)

<ol>
<li>
  <b>xgettext</b> scans the source code for calls to
  <tt>tr()</tt>, <tt>trc()</tt> and <tt>trn()</tt> and creates
  a pot file that contains all strings in the native language.
</li>
<li>
  <b>msgmerge</b> merges the strings into a po file that
  contains translations for a single locale. This file can be
  edited with convenient tools like <a
  href="http://www.poedit.org/">poedit</a>, KBabel or Emacs.
</li>
<li>
  <b>msgfmt</b> is used to generate Java class files that
  extend the Java <tt>ResourceBundle</tt> class.
</li>
</ol>

<p>Here is a simple example of running the gettext commands:</p>

```bash
# extract keys
xgettext -ktrc -ktr -kmarktr -ktrn:1,2 -o po/keys.pot src/*.java 

# merge keys into localized po file
msgmerge -U po/de.po po/keys.pot

# translate file
poedit po/de.po

# create German ResourceBundle class file in app.i18n package
msgfmt --java2 -d src/conf -r app.i18n.Messages -l de po/de.po
```

## Loading Resource Bundles
	  <p>In order to specify the name of the resource bundles you want to
	use, you create an <tt>i18n.properties</tt> file and place it in your
	application's toplevel package. All subpackages will use this
	configuration file unless they find a different one on the way up in
	the package hierarchy.</p>
	  
	<p><b>i18n.properties</b> expects the base name of the resources as
	the value of the key <tt>basename</tt>:</p>
```java
basename=app.i18n.Messages
```

For more information on how the resource lookup works, please read <a href="http://xnap-commons.sourceforge.net/gettext-commons/apidocs/org/xnap/commons/i18n/I18nFactory.html#getI18n(java.lang.Class, java.lang.String)">here</a>.</p>

## Maven Integration

<p>If Maven is used for building, the invocation of gettext can
  be easily integrated into the build process. Follow the
  appropriate link below for more information:</p>

<ul>
  <li><a
href="http://xnap-commons.sourceforge.net/m1/maven-gettext-plugin/index.html">Maven	1.x Gettext Plugin</a></li>
  <li><a
href="http://xnap-commons.sourceforge.net/m2/maven-gettext-plugin/index.html">Maven
2.x Gettext Plugin</a></li>
</ul>
