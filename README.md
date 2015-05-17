# UnifiedPreferences (uniprefs)

Unified ```SharedPreferences``` for Android. Allows to use DB, JSON in addition to XML as a low level
storage for SharedPreferences. Highly improved performance on low end devices. Fixed several known performance
and OS resources leaks problems (which is still actual for Android 5.1.x).

>
> We are stuck with technology when what we really want is just stuff that works.
>
> (c) Douglas Adams (http://ebookfriendly.com/best-technology-quotes/)
>

# States

[ ![Download](https://api.bintray.com/packages/kucherenko-alex/android/com.artfulbits%3Auniprefs/images/download.svg) ](https://bintray.com/kucherenko-alex/android/com.artfulbits%3Auniprefs/_latestVersion) [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/OleksandrKucherenko/UnifiedPreferences?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge) 

Active development, started at: 2014-01-11

Current state: on hold. Collecting devs feedback.

# Benefits / Design Goals

* Improve performance of the SharedPreferences on low-end devices
* Fix issue with threads (Each instance of SharedPreferences creates thread which is never destroyed). 'Storage' can have only one 'save thread' for a process now.
* Give ability to implement any custom de-/serialization of the data. Storage serialization can be replaced by any implementation you prefer: DB, JSON, etc.
* Statistics collecting, allows to collect some runtime counters/metrics during class usage
* Ability to store data in sqlite database
* Improved ability to store ```long strings``` in SharedPreferences
* Memory 'storage' pool. Creation of new instance of SharedPreferences is cheap.
* 'Storage' pool based on weak references. Polite to memory usage.
* Save of storage in background thread only.
* Save threads change own priority based on thread queue size.
* Actions Factory - allows to customize deeply modifications of the preferences.
* Implemented CommitsListener - allows to monitor in memory commit transaction.
* Custom names for save background threads. Easy to identify who created the thread and control it lifetime.
* Save to Disk Thread Pools max size is polite to device resources. CPU Cores count influence on Max number of available threads in thread pool.
* Merged save transactions, multiple Apply calls merged into one save to disk operation.

# Real life

This code was used in Android project with 120 millions active users and constantly growing user database.
So its heavily tested by real life. This code was developed mostly 1.5 year ago.

_Sorry, no names here._

# Gradle

```groovy
repositories {
    maven {
        url  "http://dl.bintray.com/kucherenko-alex/android"
    }
}

dependencies {
    // ALTERNATIVE:
    //   compile(group: 'com.artfulbits', name: 'uniprefs', version: '1.0.1.8', ext: 'aar')
    compile 'com.artfulbits:uniprefs:+@aar'
}
```

# Usage

For examples take look into Unit Tests. [Unit Tests - SharedPreferencesTests.java][1]


```java
private static final String UNIT_TESTS_PREFS = "uniprefs.data.unit.tests.prefs";
private static final String UNIT_TESTS_DB = "uniprefs.data.unit.tests";

public PreferencesUnified getPreferencesUnified() {
  return new PreferencesUnified(getContext(), UNIT_TESTS_PREFS, OrgJsonSerializer.Instance);
}

public SharedPreferences getDbPreferences() {
  return PreferencesToDb.newInstance(getContext(), UNIT_TESTS_DB);
}
```

# Migrate from old code

```java
private static SharedPreferences newInstance(@NonNull final Context context) {
  final String preferenceName = "<IDENTIFIER>";
  final SharedPreferences to = PreferencesToDb.newInstance(context, preferenceName);

  // if migration is needed
  if (PreferencesUnified.isPreferenceExists(context, preferenceName)) {
    final SharedPreferences from = context.getSharedPreferences(preferenceName, Context.MODE_PRIVATE);
    PreferencesUnified.copy(from, to);

    // drop the default shared preferences instance
    // NOTE: in case of troubles with async logic - use commit() instead of apply()
    from.edit().clear().apply();
  }

  // return context.getSharedPreferences(preferenceName, Context.MODE_PRIVATE);
  return to;
}
```

Replace ```<IDENTIFIER>``` by your shared preference instance name.

# License

    Copyright 2015 Oleksandr Kucherenko
    Copyright 2005-2015 ArtfulBits, Inc. (http://www.artfulbits.com)

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

[1]: uniprefs/src/test/java/com/artfulbits/uniprefs/SharedPreferencesTests.java
