Draft RFC for DotEnv configuration files

## Specification for DotEnv File Format

### Abstract

This document specifies the DotEnv file format, a widely adopted convention for managing environment variables in software applications.

The DotEnv format allows developers to define environment variables in a .env file, which can be loaded into runtime environments, thereby separating configuration from code, enhancing security, and simplifying configuration management across different environments.

### Introduction

The DotEnv file format, initially popularized for its use in web application development, has become a standard for configuration management in various programming environments. This document aims to consolidate the known rules and best practices for .env files.

### File Naming and Location

The file is typically named `.env`.
It should be placed at the root of the project or in a location accessible by the application at runtime.

### File Encoding
All DotEnv files MUST be `UTF-8` encoded, `without a BOM`.


### Key-Value Pairs
Each line represents a variable with the syntax KEY=VALUE.

Examples:

- `FirstKey=1234567890`
- `SECONDKEY="Another Value"`
- `KEY_3='Yet Another'`




### Naming Requirements for Key Names

- Keys MUST only consist of letters, digits, and underscores (_)
- Keys MUST not start with a digit.
- Keys MAY be treated case sensitive depending on the operating system. (When running under Microsoft Windows, keys MUST be treated as case insensitive)  For portability, all keys SHOULD be converted to uppercase.

- Valid: `API_KEY,DB_HOST`
- Invalid: `2SIDED`

- Regex: `[a-zA-Z_]+[a-zA-Z0-9_]*`



### WhiteSpace

- Whitespace on either side of the "=" character in a Key=Value pair MUST be ignored
- Leading whitespace MUST be ignored for all lines EXCEPT when the leading spaces are part of a multi-line value
- Trailing whitespace MUST be ignored for all lines EXCEPT when the trailing spaces are part of a multi-line value



### Comment Lines
Lines MUST be ignored when started with the pound symbol (#) EXCEPT when the pound symbol is part of a multi-line value

Examples:

- `# This is a comment`
- `### This is also a # line comment`
````
MultiLineException="This is a multi-line
value with the next line starting with # and is the odd case
# this text is included in the value"
````

### Inline Comments
Inline comments for unquoted values MUST be supported.  Some implementations MAY require a preceding space in non-quoted inline comments.

- `Key=Value #This part is a comment in all implementations`
- `Key=Value#This part is not a comment in some implementations but is in others. It is recommended not to rely on this behavior`
- `Key="Value"#This part is a comment in all implementations`
- `Key='Value'#This part is a comment in all implementations`

Pound symbols within quoted values are always part of the value:
- `Key="Value#This is not a comment"`
- `Key="Value # This is not a comment"`
- `Key='Value#This is not a comment'`
- `Key='Value # This is not a comment'`

### Quoting Values:
Quotes are not included in the resulting value.  For example, all three of these Key Value pairs have the same value:
````
Key=Value
Key='Value'
Key="Value"
````
Double quotes (") MUST allow for variable interpolation and multi-line values.

Example:

`DB_CONFIG="user=${MYUSER} dbname=${MYDBNAME}"`

Single quotes (') MUST prevent all interpolation of the key value

Example:

`MYKEY='AsIs${NotAVariable}'`

Variable Interpolation: For unquoted or double-quoted values, variable interpolation MUST be allowed using the format `{$KEY}`.

Example:

`ANOTHER_KEY="{$API_KEY}/extra"`

Note: some implementations currently allow for a shorter `$KEY` variable format


### Multi-line Values:

There are implementation variations for the support of multi-line values.  All implementations MUST support double-quoted multi-line values.

Example:
````
MULTILINE_VAR="this double-quoted value
is multi-line and should be supported along with {$KEY} variable interpolation"
````

Implementations MAY support the use of backticks to define non-interpolated multi-line values.

Example:
````
MULTILINE_VAR=`
line1 AsIs${NotAVariable}
line2
`
````



### Exceptions
Exceptions MAY be thrown when parsing invalid data.  It is recommended that parsing errors always have the opportunity to be logged.



### Security Considerations

Sensitive information (like API keys or passwords) SHOULD be encrypted or stored in a secure vault.
The .env file SHOULD NOT be committed to version control unless encrypted or containing placeholders.


### Implementation Variations and Best Practices

- Loading: Applications typically load .env files at startup.
- Fallbacks: Some implementations allow fallback to system environment variables if not defined in .env.
- Type Inference: Advanced implementations might infer or cast variable types based on content or defined schemas.



### Security and Configuration Guidelines

- Use .env for development and testing.
- For production, consider more secure methods like container secrets or cloudprovider's secret management services.
- Regularly audit .env files for sensitive information.
- It is common practice to modify in your `.gitignore` file to exclude DotEnv files to prevent confidential data from being added to your code repository:
````
# DotEnv environment settings
.env
````
- It is also common practice to include a `.env.example` or `env.template` file to act as a project template for new developers to help with onboarding.


### Acknowledgements

This draft provides a structured approach to defining the DotEnv format based on common practices. It's worth noting that while this format is widely used, it lacks an official RFC or standardization body endorsement, making this draft a hypothetical representation of current community practices.
