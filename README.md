# artifact #

[![Puppet Forge](https://img.shields.io/badge/puppetforge-v0.3.2-blue.svg)](https://forge.puppetlabs.com/swizzley88/artifact)

**Table of Contents**

1. [Overview](#overview)
2. [Setup](#setup)
3. [Parameteres](#parameters)
4. [Usage](#usage)
5. [Requirements](#requirements)
6. [Compatibility](#compatibility)
7. [Limitations](#limitations)
8. [Development](#development)

## Overviw

This module is designed to download artifacts using curl, the key feature is the ability to update artifacts from a source if they are different. It uses curl because it doesn't usually need installed. For refreshing services dependant on these artifacts... which is why this module was developed relies heavily on meta parameters, require, subscribe, before, notify, and for triggering external checks the exec refreshonly bool. As of version 0.2.x this module adds a new bash script called /usr/local/sbin/artifact-puppet, this bash script is more efficient and reliable because it does a test based on file size before downloading, and then confirms the size matches before overriding the working file with the swap file, else it prints a download error, or skips the download entirely which only applies to the ```update => true``` parameter. 

## Setup

```puppet module install csfreak-artifact```

## Paramaeters

  * $source   https://some_url.jar ```[required]```
  * $target   /some/local/dir ```[required]```
  * $update   override with newer ```[default: false]```
  * $rename   rename the downloaded file ```[default: undef]```
  * $purge    replace the target (alternative to updating) ```[default: false]```
  * $swap     /download/dir ```[default: /tmp]```
  * $timeout  exit after $x seconds ```[default: 0]```
  * $owner    uid owner of file ```[default: undef]```
  * $group    gid owner of file ```[default: undef]```
  * $mode     default permissions mode of file ```[default: undef]```
  * $validate default method to validate file ```[default: size]```
  * $modified     Prevent replacement loops if puppet modifies this file ```[default: false]```

## Usage

```
artifact { 'artifact.war': 
  source   => 'http://example.com/pub/artifact.war', 
  target   => '/home/tomcat/webapps', 
  swap     => '/home/tomcat/tmp',
  purge    => true,
  update   => false,
  timeoute => 60,
  owner    => 'tomcat',
  group    => 'tomcat',
  mode     => '0640',
  before   => File['/home/tomcat/conf/artifact.properties'],
  notify   => Service['tomcat'],
}
```

```
artifact { "artifact-${version}-${build}.war": 
  source  => "http://example.com/pub/artifact-${version}-${build}.war", 
  target  => '/home/tomcat/webapps', 
  update  => true,
}
```

Command-Line usage of /usr/local/sbin/artifact-puppet:

```
/usr/local/sbin/artifact-puppet /opt/wordpress.tar.gz https://wordpress.org/latest.tar.gz /tmp/wordpress.tar.gz size false
```


## Requirements

```
package { ['curl', 'diffutils', 'grep', 'dos2unix', 'md5sum']: ensure => 'installed' }
```

## Compatibility

Linux:

 * RHEL/CentOS/Fedora/Oracle/Scientific
 * Debian/Ubuntu
 
Tested On: CentOS 7

## Limitations

 * To preform md5sum,  remote file must have md5 available at the same url with an aditional .md5

## Source

  * All versions before 0.4 forked from https://github.com/swizzley/puppet-artifact

