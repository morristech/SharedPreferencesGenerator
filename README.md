# SharedPreferencesGenerator (SPG)
A simple tool for code generation of `android.content.SharedPreferences` based on model class described in java with a little help of annotations. Provides an ability to encapsulate data, saves time writing boiler-plate code, goes beyond SharedPreferences usage with easy-to use Serialization & default values evaluation at runtime.

### Installation

**Core**
[![Maven Central](https://img.shields.io/maven-central/v/ru.noties.spg/core.svg)](http://search.maven.org/#search|ga|1|g%3A%22ru.noties.spg%22%20AND%20a%3A%22core%22)
```groovy
compile 'ru.noties.spg:core:X.X.X'
```

**Annotations**
[![Maven Central](https://img.shields.io/maven-central/v/ru.noties.spg/annotations.svg)](http://search.maven.org/#search|ga|1|g%3A%22ru.noties.spg%22%20AND%20a%3A%22annotations%22)
```groovy
compile 'ru.noties.spg:annotations:X.X.X'
```

**Compiler**
[![Maven Central](https://img.shields.io/maven-central/v/ru.noties.spg/processor.svg)](http://search.maven.org/#search|ga|1|g%3A%22ru.noties.spg%22%20AND%20a%3A%22processor%22)
```groovy
apt 'ru.noties.spg:processor:X.X.X'
```

### Basic
Create a java object to describe your future SharedPreferences model & annotate it with `ru.noties.spg.anno.SPGPreference`. The simplest usage would be:
```java
@SPGPreference
class Simplest {
	String someString;
	long someLong;
	int someInt;
	boolean someBool;
}
```
With a little help of SPG library you will get a lof of useful information of a preference via constansts (no more magic values); human readable getters & setters; some useful methods from `ru.noties.spg.SPGPreferenceObject` interface. A generated code for the `Simplest` class would look like:
```java

// This file is generated by SharedPreferencesGenerator library at Thu Sep 03 12:13:57 FET 2015
// The description for this preference was taken from: ru.noties.spg.sample.pref.Preferences.Simplest
// Do not modify this file

public final class SimplestPreference implements SPGPreferenceObject {

    public static final String PREFERENCE_NAME = "Simplest";
    public static final int PREFERENCE_MODE = 0;

    public static final long DEF_LONG = -1L;
    public static final int DEF_INT = -1;
    public static final float DEF_FLOAT = Float.NaN;
    public static final boolean DEF_BOOL = false;
    public static final String DEF_STRING = null;

    public static final String KEY_SOME_STRING = "someString";
    public static final String KEY_SOME_LONG = "someLong";
    public static final String KEY_SOME_INT = "someInt";
    public static final String KEY_SOME_BOOL = "someBool";

    private final SharedPreferences prefs;
    private final SharedPreferences.Editor editor;

    public SimplestPreference(Context context) {
        this.prefs = context.getSharedPreferences(PREFERENCE_NAME, 0);
        this.editor = prefs.edit();
    }

    public java.lang.String getSomeString() {
        return prefs.getString("someString", DEF_STRING);
    }

    public long getSomeLong() {
        return prefs.getLong("someLong", DEF_LONG);
    }

    public int getSomeInt() {
        return prefs.getInt("someInt", DEF_INT);
    }

    public boolean isSomeBool() {
        return prefs.getBoolean("someBool", DEF_BOOL);
    }

    public void setSomeString(java.lang.String value) {
        editor.putString("someString", value).apply();
    }

    public void setSomeLong(long value) {
        editor.putLong("someLong", value).apply();
    }

    public void setSomeInt(int value) {
        editor.putInt("someInt", value).apply();
    }

    public void setSomeBool(boolean value) {
        editor.putBoolean("someBool", value).apply();
    }

    public Map<String, Object> toMap() {
        final Map<String, Object> map = new HashMap<String, Object>();
        map.put("someString", getSomeString());
        map.put("someLong", getSomeLong());
        map.put("someInt", getSomeInt());
        map.put("someBool", isSomeBool());
        return map;
    }

    public SharedPreferences getSharedPreferences() {
        return prefs;
    }

    public SharedPreferences.Editor getEditor() {
        return editor;
    }

    public String getSharedPreferencesName() {
        return PREFERENCE_NAME;
    }

    public int getSharedPreferencesMode() {
        return PREFERENCE_MODE;
    }

    public <T> T get(String key) {
        if (key == null) { return null; }
        final Object o;
        if (key.equals("someString")) {
            o = getSomeString();
        } else if (key.equals("someLong")) {
            o = getSomeLong();
        } else if (key.equals("someInt")) {
            o = getSomeInt();
        } else if (key.equals("someBool")) {
            o = isSomeBool();
        } else {
            // not in this prefs;
            o = null;
        }
        return (T) o;
    }

    public Setter setter() {
        return new Setter(editor);
    }

    public static final class Setter {

        private final SharedPreferences.Editor editor;

        Setter(SharedPreferences.Editor editor) {
            this.editor = editor;
        }

        public Setter setSomeString(java.lang.String value) {
            editor.putString("someString", value);
            return this;
        }

        public Setter setSomeLong(long value) {
            editor.putLong("someLong", value);
            return this;
        }

        public Setter setSomeInt(int value) {
            editor.putInt("someInt", value);
            return this;
        }

        public Setter setSomeBool(boolean value) {
            editor.putBoolean("someBool", value);
            return this;
        }

        public void apply() { editor.apply(); }
    }

}
```

### @SPGPreference
```java
String name() default "";
int sharedPreferenceMode() default 0;
String[] imports() default {};
boolean isSingleton() default false;
```

**name** - the output name of this shared preference. If not set, the class name would used (in the example above it will be `Simplest`)

**sharedPreferenceMode** - well, the shared preference mode

The above 2 are well known via `context.getSharedPreferences(**name**, **sharedPreferencesMode**)`

**imports** - is a simple way to actually import a certain java package/packages. This comes at handy when dealing with runtime code evaluation (on which later)

**isSingleton** - should be self describing. The only thing that should be **noted** is that if a preference is marked as a singleton, a `SPGManager.setContextProvider(ru.noties.spg.ContextProvider provider)` **must** be called first (before calling `getInstance()` method). The best place to set ContextProvider is Application's onCreate() method.

Additionally @SPGPreference gives you ability to set this-preference-wide default values (default values for a specific field could be set via @SPGKey on which a little later):
```java
String defBool() default "false";
String defInt() default "-1";
String defLong() default "-1L";
String defFloat() default "Float.NaN";
String defString() default "null";
```

### @SPGKey
Additionally each field of @SPGPreferences annotated class could be annotated with **@SPGKey**:
```java
String name() default "";
String defaultValue() default "";
Class<?> serializer() default Void.class;
boolean onUpdate() default false;
```

**name** - shared preference key name (`preference.getString("key", null)`). If not set the field's name would be used

**defaultValue** - default value for this key. Should be a string. (`"-1"`, `".0F"`, etc.). Also could be evaluated at runtime if set like: `"${System.currentTimeMillis()}"`

**serializer** - is a class of serializer if this key is serialized (must implement `SPGSerializer<TYPE, REPRESENTATION>`)

**onUpdate** - indicates that SPG library should generate a `set*OnUpdateListener()` and listen to the changes that occur with this key

### Imports & code evaluation


### Serialization

### SPGPreferenceObject


## License

```
  Copyright 2015 Dimitry Ivanov (mail@dimitryivanov.ru)

  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
```

















