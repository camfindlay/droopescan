# droopescan.

A plugin-based scanner that aids security researchers in identifying issues with
several CMSs, mainly Drupal & Silverstripe.

Stable versions can be [downloaded from here](https://github.com/droope/droopescan/releases).

[![Build Status](https://travis-ci.org/droope/droopescan.svg?branch=master)](https://travis-ci.org/droope/droopescan)

<pre>
computer:~/droopescan# ./droopescan scan drupal -u http://drupal.org/ -t 8
[+] No themes found.                                                            

[+] Possible interesting urls found:
    Default changelog file. - https://www.drupal.org/CHANGELOG.txt
    Default admin. - https://www.drupal.org/user/login

[+] Possible version(s):
    7.34

[+] Plugins found:
    views https://www.drupal.org/sites/all/modules/views/
    token https://www.drupal.org/sites/all/modules/token/
    pathauto https://www.drupal.org/sites/all/modules/pathauto/
    libraries https://www.drupal.org/sites/all/modules/libraries/
    entity https://www.drupal.org/sites/all/modules/entity/
    google_analytics https://www.drupal.org/sites/all/modules/google_analytics/
    ctools https://www.drupal.org/sites/all/modules/ctools/
    features https://www.drupal.org/sites/all/modules/features/
    views_bulk_operations https://www.drupal.org/sites/all/modules/views_bulk_operations/
    link https://www.drupal.org/sites/all/modules/link/
    metatag https://www.drupal.org/sites/all/modules/metatag/
    field_group https://www.drupal.org/sites/all/modules/field_group/
    entityreference https://www.drupal.org/sites/all/modules/entityreference/
    references https://www.drupal.org/sites/all/modules/references/
    redirect https://www.drupal.org/sites/all/modules/redirect/
    field_collection https://www.drupal.org/sites/all/modules/field_collection/
    diff https://www.drupal.org/sites/all/modules/diff/
    flag https://www.drupal.org/sites/all/modules/flag/
    search_api https://www.drupal.org/sites/all/modules/search_api/
    migrate https://www.drupal.org/sites/all/modules/migrate/
    honeypot https://www.drupal.org/sites/all/modules/honeypot/
    facetapi https://www.drupal.org/sites/all/modules/facetapi/
    jcarousel https://www.drupal.org/sites/all/modules/jcarousel/
    search_api_db https://www.drupal.org/sites/all/modules/search_api_db/
    security_review https://www.drupal.org/sites/all/modules/security_review/
    memcache https://www.drupal.org/sites/all/modules/memcache/
    views_field_view https://www.drupal.org/sites/all/modules/views_field_view/
    field_formatter_settings https://www.drupal.org/sites/all/modules/field_formatter_settings/
    r4032login https://www.drupal.org/sites/all/modules/r4032login/
    varnish https://www.drupal.org/sites/all/modules/varnish/
    bueditor https://www.drupal.org/sites/all/modules/bueditor/
    apachesolr https://www.drupal.org/sites/all/modules/apachesolr/
    codefilter https://www.drupal.org/sites/all/modules/codefilter/
    geolocation https://www.drupal.org/sites/all/modules/geolocation/
    url https://www.drupal.org/sites/all/modules/url/
    views_content_cache https://www.drupal.org/sites/all/modules/views_content_cache/
    homebox https://www.drupal.org/sites/all/modules/homebox/
    fasttoggle https://www.drupal.org/sites/all/modules/fasttoggle/
    views_litepager https://www.drupal.org/sites/all/modules/views_litepager/

[+] Scan finished (0:08:11.388810 elapsed)
</pre>

You can get a full list of options by running:

<pre>
python droopescan.py --help
python droopescan.py scan --help
</pre>

# Why droopescan?

* Fast, multi-threaded scanning. Mass scanning supported out of the box.
* Scans for outdated installations, and also themes and plugins.
* Plugin system which handles not only scanning but also the
[updating](https://github.com/droope/droopescan/blob/3f7c786b99eef40b53dc3ef541772d66684a20da/plugins/drupal.py#L36-L55)
of plugins.
* Support for basic authentication and intercepting proxies.

# Installation

Installation is as follows:

<pre>
git clone https://github.com/droope/droopescan.git
cd droopescan
pip install -r requirements.txt
./droopescan scan --help
</pre>

The master branch always contains the latest version released.

# Features
## Scan types.

Droopescan aims to be the most accurate by default, while not overloading the
target server due to excessive concurrent requests. Due to this, by default, a
large number of requests will be made with four threads; change these settings
by using the `--number` and `--threads` arguments respectively.

This tool is able to perform four kinds of tests:

* *Plugin checks*: Performs several thousand HTTP requests and returns a
listing of all plugins found to be installed in the target host.
* *Theme checks*: As above, but for themes.
* *Version checks*: Downloads several files and, based on the checksums of these
files, returns a list of all possible versions. 
* *Interesting url checks*: Checks for interesting urls (admin panels, readme
files, etc.)

## Authentication.

The application fully supports `.netrc` files and `http_proxy` environment
variables. 

You can set the `http_proxy` and `https_proxy` variables. These allow you to
set a parent HTTP proxy, in which you can handle more complex types of
authentication (e.g. Fiddler, ZAP, Burp)

<pre>
export http_proxy='localhost:8080'
export https_proxy='localhost:8080'
python droopescan.py drupal --url http://localhost/drupal
</pre>

Another option is to use a .netrc file for basic authentication. An example
`~/.netrc` file could look as follows:

<pre>
machine secret.google.com
    login pedro.worcel@security-assessment.com
    password Winter01
</pre>

*WARNING:* By design, to allow intercepting proxies and the testing of
applications with bad SSL, droopescan allows self-signed or otherwise invalid
certificates. ˙ ͜ʟ˙

## Output.

This application supports both "standard output", meant for human consumption,
or JSON, which is more suitable for machine consumption.

This can be controlled with the `--output` flag. Some sample JSON output would
look as follows (minus the excessive whitespace):

<pre>
{
  "themes": {
    "is_empty": true,
    "finds": [
      
    ]
  },
  "interesting urls": {
    "is_empty": false,
    "finds": [
      {
        "url": "https:\/\/www.drupal.org\/CHANGELOG.txt",
        "description": "Default changelog file."
      },
      {
        "url": "https:\/\/www.drupal.org\/user\/login",
        "description": "Default admin."
      }
    ]
  },
  "version": {
    "is_empty": false,
    "finds": [
      "7.29",
      "7.30",
      "7.31"
    ]
  },
  "plugins": {
    "is_empty": false,
    "finds": [
      {
        "url": "https:\/\/www.drupal.org\/sites\/all\/modules\/views\/",
        "name": "views"
      },
      [...snip...]
    ]
  }
}
</pre>

Some attributes might be missing from the JSON object if parts of the scan are
not ran. 

This is how multi-site output looks like; each line contains a valid JSON object
as shown above.

<pre>
    $ ./droopescan scan drupal -U six_and_above.txt -e v
    {"host": "http://localhost/drupal-7.6/", "version": {"is_empty": false, "finds": ["7.6"]}}
    {"host": "http://localhost/drupal-7.7/", "version": {"is_empty": false, "finds": ["7.7"]}}
    {"host": "http://localhost/drupal-7.8/", "version": {"is_empty": false, "finds": ["7.8"]}}
    {"host": "http://localhost/drupal-7.9/", "version": {"is_empty": false, "finds": ["7.9"]}}
    {"host": "http://localhost/drupal-7.10/", "version": {"is_empty": false, "finds": ["7.10"]}}
    {"host": "http://localhost/drupal-7.11/", "version": {"is_empty": false, "finds": ["7.11"]}}
    {"host": "http://localhost/drupal-7.12/", "version": {"is_empty": false, "finds": ["7.12"]}}
    {"host": "http://localhost/drupal-7.13/", "version": {"is_empty": false, "finds": ["7.13"]}}
    {"host": "http://localhost/drupal-7.14/", "version": {"is_empty": false, "finds": ["7.14"]}}
    {"host": "http://localhost/drupal-7.15/", "version": {"is_empty": false, "finds": ["7.15"]}}
    {"host": "http://localhost/drupal-7.16/", "version": {"is_empty": false, "finds": ["7.16"]}}
    {"host": "http://localhost/drupal-7.17/", "version": {"is_empty": false, "finds": ["7.17"]}}
    {"host": "http://localhost/drupal-7.18/", "version": {"is_empty": false, "finds": ["7.18"]}}
    {"host": "http://localhost/drupal-7.19/", "version": {"is_empty": false, "finds": ["7.19"]}}
    {"host": "http://localhost/drupal-7.20/", "version": {"is_empty": false, "finds": ["7.20"]}}
    {"host": "http://localhost/drupal-7.21/", "version": {"is_empty": false, "finds": ["7.21"]}}
    {"host": "http://localhost/drupal-7.22/", "version": {"is_empty": false, "finds": ["7.22"]}}
    {"host": "http://localhost/drupal-7.23/", "version": {"is_empty": false, "finds": ["7.23"]}}
    {"host": "http://localhost/drupal-7.24/", "version": {"is_empty": false, "finds": ["7.24"]}}
    {"host": "http://localhost/drupal-7.25/", "version": {"is_empty": false, "finds": ["7.25"]}}
    {"host": "http://localhost/drupal-7.26/", "version": {"is_empty": false, "finds": ["7.26"]}}
    {"host": "http://localhost/drupal-7.27/", "version": {"is_empty": false, "finds": ["7.27"]}}
    {"host": "http://localhost/drupal-7.28/", "version": {"is_empty": false, "finds": ["7.28"]}}
    {"host": "http://localhost/drupal-7.29/", "version": {"is_empty": false, "finds": ["7.29"]}}
    {"host": "http://localhost/drupal-7.30/", "version": {"is_empty": false, "finds": ["7.30"]}}
    {"host": "http://localhost/drupal-7.31/", "version": {"is_empty": false, "finds": ["7.31"]}}
    {"host": "http://localhost/drupal-7.32/", "version": {"is_empty": false, "finds": ["7.32"]}}
    {"host": "http://localhost/drupal-7.33/", "version": {"is_empty": false, "finds": ["7.33"]}}
    {"host": "http://localhost/drupal-7.34/", "version": {"is_empty": false, "finds": ["7.34"]}}
</pre>

## Debug.

When things are not going exactly your way, you can check why by using the
`--debug-requests` command. 

Some output might look like this:

<pre>
computer:~/droopescan# ./droopescan scan silverstripe -u http://localhost -n 10 -e p --debug-requests
[head] http://localhost/framework/... 403
[head] http://localhost/cms/css/layout.css... 404
[head] http://localhost/framework/css/UploadField.css... 200
[head] http://localhost/misc/test/error/404/ispresent.html... 404
[head] http://localhost/widgetextensions/... 404
[head] http://localhost/orbit/... 404
[head] http://localhost/sitemap/... 404
[head] http://localhost/simplestspam/... 404
[head] http://localhost/ecommerce_modifier_example/... 404
[head] http://localhost/silverstripe-hashpath/... 404
[head] http://localhost/timeline/... 404
[head] http://localhost/silverstripe-hiddenfields/... 404
[head] http://localhost/addressable/... 404
[head] http://localhost/silverstripe-description/... 404
[+] No plugins found.

[+] Scan finished (0:00:00.058422 elapsed)
</pre>

There is another argument, `--debug`, which is added automatically by [cement](http://builtoncement.com/) 
and is not what you want.

## Stats.

You can get an up to date report on the capabilities of the scanner by running
the following command

<pre>
    ./droopescan stats
</pre>

Some sample output might look as follows:

<pre>
Functionality available for 'Drupal':
    - Enumerate plugins (XXX plugins, last updated X months ago)
    - Enumerate themes (XX themes, last updated X months ago)
    - Enumerate interesting urls (X urls)
    - Enumerate version (up to version X.X.X)

Functionality available for 'SilverStripe':
    - Enumerate plugins (XXX plugins, last updated X months ago)
    - Enumerate themes (XX themes, last updated X months ago)
    - Enumerate interesting urls (X urls)
    - Enumerate version (up to version X.X.X)
</pre>

# Contribute.

## Create your own plugin.

You can add suport for your favourite CMS. The process is actually quite
simple, and a lot of information can be glimpsed by viewing the example.py file
in the plugins/ folder.

This file should serve well as a base for your implementation.

You can create your own plugin for `Joomla` and enable it as follows:

<pre>
$ cp plugins/example.py plugins/joomla.py
$ cp plugins.d/example.conf plugins.d/joomla.conf
</pre>

You then need to go to `plugins/joomla.py` and change a few things:

- The class name needs to be Joomla.
- The plugin label (located at Meta.label) needs to be changed to joomla.
- At the end of the file, the register call needs to be modified to reflect the
  correct class name.
- The exposed function, 'example', needs to be renamed to joomla.

<pre>
    @controller.expose(help='example scanner')
    def joomla(self):
        self.plugin_init()
</pre>

We also need to change the `plugins.d/joomla.conf` file, and change it to the
following:

<pre>
[joomla]
enable_plugin = true
</pre>

We should now be in a state which looks as follows:

<pre>
$ ./droopescan scan joomla
[+] --url parameter is required.
</pre>

Your next step would be to generate a valid plugin wordlist, a valid theme
wordlist, a
[versions.xml](https://github.com/droope/droopescan/blob/development/plugins/drupal/versions.xml)
file, and optionally a list of interesting URLs, as well as replace all variables
that are in joomla.py with values that are correct for your implementation.

## Issues & Pull Requests.

Pull requests that create new plugins are welcome provided that maintenance for
those plugins is done automatically (i.e. the plugins implement
update_version_check and update_version).

Pull requests are welcome. Please remember to make your pull requests against
the develoment branch rather than the master. Issues can be raised on the issue
tracker here on GitHub.

To run tests, some dependencies must be installed. Running the following
commands will result in them being installed and the tests being ran:

<pre>
    apt-get install python-dev libxslt1-dev libxml2-dev python3
    pip install -r requirements_test.txt
    ./droopescan test
</pre>

# License.

The project is licensed under the GPL license.
