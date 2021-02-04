# Morphing #define's macros to a structure

I encountered a scenario where we had hundreds of #define'd values defining a key path
and extern variables in a header and their corresponding definitions in a c file.

In addition to that of course, these #defines were used throughout the code-base after
programmers broke the guideline of creating functions that used these macros.

This was primed for unintentional abuse.

I worked on a way to mitigate the mess we created over the years, but this required me
to correct tens of thousands of lines of code. What a better way to do this than using
bash scripting.

Example (not actual code):


```
#define CONFIG_SOME_KEY_VALUE "my:key"

...

const extern int CONFIG_DEFAULT_SOME_KEY_VALUE;

...

const int CONFIG_DEFAULT_SOME_KEY_VALUE = 30;

...

int
getValue(const char* key, int defaultValue)
{
	int v = defaultValue;
	getValue(key, v);
	return v;
}

...

int mySetting = getValue(CONFIG_SOME_KEY_VALUE, CONFIG_DEFAULT_SOME_KEY_VALUE);

LOG_DEBUG("Blah blah blah [" << CONFIG_SOME_KEY_VALUE << "]");

```

1. Converted CNF_SOME_KEY_VALUE from a string literal to a structure containing key and value.
2. Eliminated use of CONFIG_DEFAULT_XYZ variables.
3. Where CONFIG_SOME_KEY_VALUE in use, replace with CONFIG_SOME_KEY_VALUE.path where appropriate.
   This step had to be manual at times because additional changes were needed to places that used it.
4. Where CONFIG_DEFAULT_KEY_VALUE in use, replace with CONFIG_SOME_KEY_VALUE.preset.

For #4, used the following one-liner for a targetted approach:

```
$ export KEY="CONFIG_SOME_KEY_VALUE" ; sgrep "$KEY" * | cut -f1 -d':' | uniq | while read F; do sed -i -e "s/$KEY/${KEY/_DEFAULT/}\.preset/" $F; done
```

Note: sgrep is an alias.
Note: omitted details like the default values having different types, even typedefs, and other details
      about using them in both C and C++ code.

