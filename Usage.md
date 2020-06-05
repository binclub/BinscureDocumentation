# Usage
A good binscure config is made up of four main components:

## Input/Output
Example:
```Yaml
input: test.jar
output: test-obf.jar
```
Output is not required, and will default to [input]-obf.jar

## Libraries
Libraries are required for the remapper to work. If you have a class ClassA that extends ClassB, and ClassB is not 
present in the input jar, then a jar containing ClassB *must* be present in the libraries for binscure to function
normally. Binscure will not force you to include all referenced classes, however if you dont it can break
functionality.

Example:
```Yaml
libraries:
	- library1.jar
	- library2.jar
```

Unlike other obfuscators it is not necessary to include the JRE in the libraries.

## Exclusions
Binscure allows you to define a list of classes that will be totally excempt from all obfuscation.
This is made up of two types, exclusions and hard exclusions. 

Exclusions will be processsed by binscure, even if they are not "obfuscated". You should use this for classes
that are part of your program, but that you do not want obfuscated.

Example:
```Yaml
exclusions:
	- com/binclub/binscure/Main # This is a class
	- com/binclub/binscure/api/ # This is a package
```

Hard exclusions will not be processed whatsoever, reducing the total time taken to obfuscate. You should use this
for third party libraries included in your final jar.

Example:
```Yaml
hardExclusions:
	- javassist/
	- com/google/
	- kotlin/
```

## Transformers
Example:
```Yaml
# Handles renaming
remap:
	enabled: false
	classes: true
	methods: true
	fields: true
	localVariables: true
	classPrefix: "com/project/"
	methodPrefix: ""
	fieldPrefix: ""
	localVariableName: "" # Empty to remove local variables
	# Will aggressively overload member names with different descriptions
	aggressiveOverloading: false 
	obfuscateEnums: true

# Strip source file information
sourceStrip:
	enabled: false
	# Default is REMOVE, options [KEEP/REMOVE]
	lineNumbers: REMOVE

# Removes additional data created by the kotlin compiler
# WARNING: Can break the kotlinx.reflect library
kotlinMetadata:
	enabled: false
	# Options: REMOVE/CENSOR
	type: CENSOR

# Employs various tactics to limit or remove functionality from reverse engineering libraries
crasher:
	enabled: false
	 # Exploits various ZIP standard vulnerabilities
	checksums: false
	# Attemps to break the ASM library
	antiAsm: false

# Encrypts constants constants and decrypts them dynamically at runtime
# Uses Context checking and may impact application startup time
stringObfuscation:
	enabled: false

# Obscure the control flow of the program
flowObfuscation:
	enabled: true
	# Lower = more severe, higher file size, less performance
	# Higher = less severe, lower file size, more performance
	severity: 7
	# Options: NONE, BLOAT_CLASSES
	mergeMethods: BLOAT_CLASSES

optimisation:
	enabled: true
	# Enums by default store their available values in a private `values` field. This field is cloned and returned using
	# the "values()" method. Cloning this array can introduce performance and memory issues.
	# This transformer removes the array cloning, increasing peformance but potentially allowing unsafe modification of enum values
	mutableEnumValues: false
```

## Other options
There are some other extra options that you can set:
```Yaml
# Disable "WARNING: class was not found in the classpath" warnings
# Default is false
ignoreClassPathNotFound: false

# A file path where a .csv mappings file will be saved
mappingFile: null
```

## Full Example
```Yaml
input: test.jar
output: test-obf.jar

libraries:
	- library1.jar
	- library2.jar

hardExclusions:
	- kotlin/

exclusions:
	- dev/binclub/test/api

remap:
	enabled: true
	classPrefix: "dev/binclub/"
	localVariableName: ""
	aggressiveOverloading: false

sourceStrip:
	enabled: true
	lineNumbers: REMOVE

kotlinMetadata:
	enabled: true

crasher:
	enabled: true
	antiAsm: true
	checksums: true

stringObfuscation:
	enabled: true

flowObfuscation:
	enabled: true
	severity: 6
	mergeMethods: NONE

ignoreClassPathNotFound: false
mappingFile: mappings.csv
```

## Minecraft Forge
If obfuscating for minecraft forge make sure to include `forge-version-binpatched.jar` and `forge-version-srgBin.jar` in your libraries.
