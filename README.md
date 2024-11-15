# Human Readable Archive (hra)

A file that contains directory information, file contents, and meta-data.

Example:
```hra
Human Readable
Archive
0.1
meta= comment# redefine~ escape\ assignment= continuation\ opener" closer" encoding$ format@ trailing_ standard=hfs+
# The first two lines are more expressive than they seem. They define the type of whitspace character and newline string used in the rest of the file.
# The third line holds the file format version.
# The forth line defines the:
# - optional meta operator is assigned to `=`
# - optional comment operator is assigned to `#`
# - optional redefine operator is assigned to `~`
# - optional escape operator is assigned to `\`
# - optional attribute assignment operator assigned to `=`
# - optional line continuation operator assigned to `\`
# - optional opener operator for contiguous strings is assigned to `"`
# - doubly optional closer of contiguous strings is assigned to '"'
# - optional file encoding operator is assigned to "$"
# - optional file format operator is assigned to "@"
# - optional file trailing operator is assigned to "_"
# - optional attribute standard of `hfs+` enabled

# Explicitly define permissions for a directory
= / perm=rwxr-xr-x user=root group=wheel

# A simple text file with explicite attributes
# The attributes are assumed from the enclosing directory
= /ABC.txt
ABCDEFGHIJKLM
NOPQRSTUVWXYZ

# A simple text file with explicite attributes
= /abc.txt perm=rw-rw-rw- user=bob group=bob
abcdefghijklm
nopqrstuvwxyz

# A file with spaces in the path.
# The attributes for the `text` directory are assumed from its ancestors' since it is not explicitly stated.
# If there are no ancestor directories to reference, the attributes are assumed from its decendants'.
= "/texts/Psalm 13 KJV.txt" perm=rw-r--r-- user=bob group=bob
13 How long wilt thou forget me, O Lord? for ever? how long wilt thou hide thy face from me?

2 How long shall I take counsel in my soul, having sorrow in my heart daily? how long shall mine enemy be exalted over me?

3 Consider and hear me, O Lord my God: lighten mine eyes, lest I sleep the sleep of death;

4 Lest mine enemy say, I have prevailed against him; and those that trouble me rejoice when I am moved.

5 But I have trusted in thy mercy; my heart shall rejoice in thy salvation.

13 How long wilt thou forget me, O Lord? for ever? how long wilt thou hide thy face from me?

2 How long shall I take counsel in my soul, having sorrow in my heart daily? how long shall mine enemy be exalted over me?

3 Consider and hear me, O Lord my God: lighten mine eyes, lest I sleep the sleep of death;

4 Lest mine enemy say, I have prevailed against him; and those that trouble me rejoice when I am moved.

5 But I have trusted in thy mercy; my heart shall rejoice in thy salvation.

6 I will sing unto the Lord, because he hath dealt bountifully with me.

# A trailing `/` is required to specify a directory
= /texts/shakespere/

# An executable file
= "/texts/shakespere/Player and the Pipe" perm=rwxrwxr-x user=sue group=sue
# This is a comment. The below escape character escapes the comment operator so it is included in the file data.
\#!/bin/bash
RED='\033[0;31m'; GREEN='\033[0;32m'; BLUE='\033[0;34m'; NC='\033[0m'
play_note() { echo -e "${BLUE} Playing note: $1 ${NC}"; sleep 0.5; }
attempt_manipulation() {
    echo -e "${RED}Attempting to manipulate...${NC}"
    [[ $((RANDOM % 2)) -eq 0 ]] && echo "But these cannot I command to any utterance of harmony..." || 
    echo "'Sblood, do you think I am easier to be played on than a pipe?"
    return 1
}
echo "== The Player and the Pipe ==="
while true; do
    echo -e "\nWhat would you attempt?\n1. Play the pipe honestly\n2. Attempt to manipulate\n3. Exit"
    read -p "Choose your action (1-3): " choice
    case $choice in
        1) echo -e "\n${GREEN}Attempting to play the pipe with genuine intent...${NC}"
           for note in do re mi fa sol; do play_note $note; done
           echo "The pipe responds to honest interaction.";;
        2) echo; attempt_manipulation; echo "You cannot play upon me.";;
        3) echo -e "\nExiting - Though you can fret me, you cannot play upon me."; exit 0;;
        *) echo "Invalid choice. Please select 1-3.";;
    esac
done

# Symlink
= /link.txt target=/abc.txt perm=rwxrwxrwx user=root group=wheel

# `=^` creates the directory context `^`
=^ /contains/the/below/

# `=^` uses the directory context `^`
# the normalized file path is `/contains/the/below/README.md`
=^ README.md
# Title

# `=^-` uses the directory context `^` and creates the directory context `-`
# the normalized directory path is `/contains/the/below/src/`
=^- src/

# `=-` uses the directory context `-`
# the nomalized directory path is `/contains/the/below/src/lib.c`
=- lib.c
void lib() {}

# `=^*` uses the directory context `^` and creates the directory context `*`
# the normalized directory path is `/contains/the/below/test/`
=^* test/

# `=^-` uses the directory context `^` and recreates the directory context `-`
# the normalized directory path is `/contains/the/below/app/`
=^- app/

# `=-` uses the directory context `-`
# the nomalized directory path is `/contains/the/below/app/main.c`
=- main.c
int main() {return 0;}

# `=*` uses the directory context `*`
# the nomalized directory path is `/contains/the/below/test/test.c`
=* test.c
void test() {}

# `=^` uses the directory context `^`
# the normalized file path is `/contains/the/below/LICENSE`
=^ LICENSE
MIT License...

~ meta@ comment; redefine
; This is a comment. The above redefines the meta and comment operators characters to be `@` and `;`
; It also undefines the `redefine` operator which can longer be used.
; The comment operator can also be undefined in the same way.

@ /files/and/directories/are/now/defined/with/the/@/prefix.txt

; The below directory definition shows how attributes can be place on following lines using line ending back slash for readability.
; The leading spaces are optional.
; The meta definition ends when the last line does not have an ending back slash.
@ /multiple/line/ \
    perm=rwxrwxrwx \
    atime=2024/11/4-10:47:44
```

## Format

### Header

The header is four lines and defines how to how to interpret the rest of the file.

#### First and Second Line

The file must start with the string `Human`. Then:
1. One character to be used as the space character for the rest of the file
    - This documentation refers to this character as **space**
    - Typically, it is the *space* `0x20` or *tab* `0x09` character. Tab may be a good choice if your paths contain many `0x20` *spaces*.
1. The string `Readable`
1. The character string after `Readable` and before `Archive` defines the newline string for the rest of the document
1. The string `Archive`

#### Third (Version) Line

The third line holds the hra file format version (e.g. `0.1`).

The line contains:
1. the major version digits (version numbers us base 10 digits)
1. a `.` separator
1. the minor version digits

View the (Format Version)[#format-version) section for the version meaning.

#### Fourth (Archive Configuration) Line

The line contains the assignments:
- `meta`: Optional. The operator that indicates the line specifies a new file or directory is being defined. Can be indicated without value if `redefine` is specified and assigned.
- `comment`: Optional. The operator that indicates the line is a comment. Can be indicated without value if `redefine` is specified and assigned.
- `redefine`: Optional. The operator that indicates the line specifies operator redefinitions. If indicated, must be assigned a value in this line but may be undefined in later redefinitions.
- `escape`: Optional. The operator that allows the operators to begin the line in file data. Can be indicated without value if `redefine` is specified and assigned.
- `assignment`: Optional. The operator that separates the attribute's name and value. Can be indicated without value if `redefine` is specified and assigned.
- `continuation`: Optional. The operator that appears at the end of a line which indicates a line continuation. Can be indicated without value if `redefine` is specified and assigned.
- `opener`: Optional. The operator that is indicates the beginning of a contiguous string that only ends with the closer operator. Can be indicated without value if `redefine` is specified and assigned.
- `closer`: Doubly Optional. If undefined, its value is the same as `opener`. It is also the only thing the escape operator can escape in the contiguous string. Can be indicated without value if `redefine` is specified and assigned.
- `encoding`: Optional. The operator that indicates content encoding for a file. If undefined, content encoding cannot be specified. Can be indicated without value if `redefine` is specified and assigned.
- `format`: Optional. The operator that indicates the final file format after extraction by specifying both text encoding and optionally line ending format. If undefined, content is stored and extracted as UTF-8 with original line endings preserved. Can be indicated without value if `redefine` is specified and assigned.
- trailing: Optional. The operator that indicates the number of trailing newlines to append to file content. The value must be a base-10 number. If undefined, text files default to one trailing newline and binary files default to zero. Can be indicated without value if `redefine` is specified and assigned.
- `standard=`: Optional. Comma-separated list of attribute standards to apply. Controls what attributes are valid throughout the archive. Can be indicated without value if `redefine` is specified and assigned.

Parser Capability Requirements:
- All operators that will be used anywhere in the file MUST be indicated in this line for parser capability checks
- Any attempt to use or redefine an operator that was not at least indicated in this line MUST result in a parse error

To define the operators:
1. The operator name
1. No separating spaces
1. The operator string. It may not contain spaces.
1. Optionally, to have more operator assignments the following can be repeated:
    1. A single space
    1. More operator assignments as described in the steps above.

For example, `meta= comment// escape123` defines the:
- Meta operator to be `=`
- Comment operator to be `//`
- Escape operator to be `123`
- The other operators are undefined and unusable

### Content

#### Meta Line

The meta line defines a path for a file or directory and its attributes.

The meta operator is defined with the `meta` assignment.

The definition at a minimum requires the following in order on the same line:
1. The meta operator
1. An optional directory context, which contains no space
1. A single space
1. A POSIX path, optionally bracketed
    - If referencing an already defined directory context, the path must not begin with a forward slash
    - If not bracketed, spaces may not be used
    - Path must not contain `.` or `..` path components
    - Path must not contain consecutive forward slashes (`//`)
 
If the POSIX path ends with trailing single forward slash, a directory is specified and not a file.

If the `encoding` operator is defined, zero or more encodings can be specified after the path:
1. A single space
1. The encoding operators
1. The encoding name, optionally bracketed
1. Optionally, steps 1-2 can be repeated for additional encodings which are applied left to right

If the `format` operator is defined, zero or more format specifications can be specified after any encodings:
1. A single space
2. The format operator
3. The format specification name, optionally bracketed

Optionally (there are no required attributes), attributes can be associated with the definition with:
1. At least one leading spaces
1. Optionally, to allow multi-line attribute assignments the following the following can be included:
    1. The line continuation operator
    1. Zero or more spaces
1. The attribute name string, optionally bracketed
1. The attribute assignment operator
1. The attribute value string, optionally bracketed
1. Optionally, the above steps can be repeated for more attribute assignments

#### Data Lines

The file data need to be preceded file meta line.

The lines following the file meta line can be data lines.
Data lines can be intermixed with lines that begin with comments and redefinitions that should not be included in the data.

To have the
- meta
- comment
- redefine
- continuation
- trailing
operators at the beginning of a data line, the escape operator must precede the operator to allow them to be included in data.

File data ends at the last non-empty data line.

The data lines following a file meta line are parsed according to these rules:
1. Each line must contain:
   - Zero or more characters that are not newline characters
   - A newline character sequence as defined in the header
1. If the data line begins with:
   - The meta operator (if defined)
   - The comment operator (if defined)
   - The redefine operator (if defined)
   - The trailing operator (if defined)
   Then the escape operator (if defined) must precede it to include it as data
1. Empty lines are preserved as part of the data unless they appear after the last non-empty data line. If there are no non-empty data lines then no empty lines are preserved.
1. Data collection ends at either:
   - The next meta line (if defined)
   - A trailing operator line (if defined)
   - The end of the file

#### Comments

Comments allow for information about the archive to be conveyed to the reader without affecting the data described in the archive.

The comment operator is defined with the `comment` assignment and is optional.
If undefined, comments cannot be used.

Comments can only be started at the beginning of the line and continue to the end of the line.
The comment operator must begin the line.

When using line continuation, comment lines are allowed between the continued lines and are ignored when assembling the logical line. For example:

Comments can appear intermixed in data lines.
See escaping in the [Data Lines](#data-lines) section to include lines beginning with the comment operator string in the file data.

The definition requires the following in order on the same line:
1. The comment operator at the beginning of the line
1. Zero or more characters
1. A newline character

Example:
```hra
# This is a comment about the file
= example.txt
Regular file content
\# This is file data because it's escaped
# This is a comment within the file data
More content

# Changing comment operator
~ comment//
// Now using different comment style
= other.txt
// These are valid comments
Content here
\// This is escaped data, not a comment

= /example.txt \
# This is a valid comment between continued lines
    perm=rwxr-xr-x \
# Another comment before the final part
    user=root
```

#### Redefinitions

Operators can be redefined throughout the document.

The redefinition operator is defined with the `redefine` assignment.
If included in the header `redefine` must assigned a value or it is a parse error.
If undefined, redefinitions cannot be used.

Redefinitions are indicated by a line starting with the redefine operator.
There can be multiple redefinitions in the same line.

Beyond redefining, operators can also be undefined, including `redefine` by not providing an assignment string.

Redefinitions can appear intermixed in data lines.
See escaping in the [Data Lines](#data-lines) section to include lines beginning with the redefine operator string in the file data.

The redefinition line requires the following in order:
1. The redefinition operator at the start of the line
1. Zero or more redefinitions, each consisting of:
   a. A single space
   a. The operator name to be redefined, optionally bracketed
   a. The operator assignment operator if providing a new value
   a. The new operator string if providing a new value, optionally bracketed

Examples
```hra
# Basic redefinition
~ meta@ comment;

# Multiple redefinitions on one line
~ meta@ comment# escape\ opener[ closer]

# Undefining operators
~ comment escape    # Removes comment and escape operators

# Using continuation for readability
~ meta@ \
  comment# \
  escape\\ \
  opener[ \
  closer]

# Redefining to multi-character operators
~ meta=== comment### escape\\\
```

#### Trailing Newlines

Trailing newlines can be specified with the `trailing` operator.
The trailing operator is followed by the base ten encoded number to explicitly specify the number of trailing newlines to append after the last non-empty data line.
Any empty lines between the last non-empty data line and the trailing operator are NOT considered part of the file data.
This allows for cleaner file organization while preserving exact trailing whitespace when needed.

See escaping in the [Data Lines](#data-lines) section to include lines beginning with the trailing operator string in the file data.

Default Values:
   - Text files (determined by content and/or decoders): 1 trailing newline
   - Binary files (determined by encoders): 0 trailing newlines
   - The default can be overridden by explicit trailing operator

Trailing newlines do not follow the attribute inheritance chain since inheritance could cause unexpected content modifications. Additionally, different files in the same directory often need different trailing newlines based on their content and purpose, making inheritance impractical and potentially harmful to file integrity.

Directories, being containers rather than content, cannot have trailing newlines. The trailing operator is invalid when used in directory definitions and any attempt to specify trailing newlines for a directory will result in a parse error. This restriction maintains the clear distinction between file content handling and directory structure.


Examples
```hara
# Empty lines before trailing are NOT included
= example1.txt
Content here


_1  # Results in: "Content here\n"

# Lines with whitespace, not newlines, before trailing ARE included
= example2.txt
Content here
# Below line has spaces
   
_1
# Results in: "Content here\n   \n"

# Multiple cases demonstrated
= example3.txt
Last content line

  
# Comment line
    
_2
# Results in: "Last content line\n\n"

= example4.txt
Data here

    
# The empty lines above are ignored
_0
# Results in: "Data here"
```

## Character Encoding

The Human Readable Archive format uses UTF-8 character encoding without Byte Order Mark (BOM).
The absence of BOM is required to ensure the file begins directly with "Human" as specified in the header requirements.
All implementations must support UTF-8 encoding to ensure universal compatibility and proper handling of international characters.

The parser MUST validate that all text in the archive is valid UTF-8 according to RFC 3629.
This strict validation ensures archive consistency and prevents any ambiguity about the stored data.

## String Bracketing

The HRA format supports string bracketing to allow the inclusion of spaces and other special characters.
A bracketed string is one that begins with the `opener` operator and ends with the `closer` operator.
If the `closer` operator is undefined, the `opener` operator also defines the `closer` operator if it is defined

Bracketing is particularly useful for:
- Paths containing spaces
- Paths containing special characters that might otherwise require escaping
- Paths containing the operator that could be misinterpreted if unbracketed.

The syntax for a bracketed string:
1. The opener operator
1. Zero or more of the following:
  - the escape operator followed by the closer operator
  - a character
1. The closer operator

### Examples

```hra
opener" escape\ comment# redefine~
# Above operator assignments: Double quote as both opener and closer, backslash as escape, hash as comment, tilde as redefine

# Valid bracketed paths:
# Spaces allowed in path
= "Program Files/example.txt"
# Another space-containing path
= "My Documents/report.doc"
# Escaped quote within path
= "Users/John\"Smith/file.txt"

# redefine operator assignments with different opener and closer:
# Angle brackets as opener/closer
~ opener< closer>

Valid bracketed paths:
# Spaces allowed in path
= <C:/Users/John Doe/file.txt>
# Multiple spaces handled
= <data files/raw data.csv>
# Escaped > within path
= <data\>files/example.txt>
```

## Format Version

The file format employs a two-number versioning scheme (MAJOR.MINOR) to clearly
communicate compatibility and feature changes to both developers and software.
The MAJOR number increments when structural changes make the format
incompatible with previous versions—allowing software to quickly determine if
it can process the file.  The MINOR number increments when new features are
added while maintaining backward compatibility; this enables older software to
still read newer files, even if it cannot utilize all features.  This approach
helps developers quickly understand how different versions relate to each other
and gives software clear rules for handling files of different versions.

## Directory Context

Directory context is a feature that allows for more concise file and directory definitions when working with deeply nested paths. It uses context modifiers with the meta operator to establish and reference directory contexts, making it easier to organize related files.

### Context Modifiers

Context modifiers are single characters that can be appended to the meta operator to establish or reference directory contexts. While the examples show `^`, `-`, and `*`, any character can be used as a context modifier. The creator of the archive can choose whatever characters make sense for their use case.

### Usage Rules

1. Creation of Context:
   - `={modifier}` followed by a full path creates a new context identified by that modifier
   - The path must be absolute (start with `/`)
   - Multiple contexts can exist simultaneously using different modifiers

1. Reference of Context:
   - `={modifier}` followed by a relative path uses the previously established context
   - The relative path is appended to the context path
   - The path must not begin with a forward slash

1. Context Redefinition:
   - An existing context can be redefined by creating a new context with the same modifier using the same syntax as a creation of context
   - After redefinition, all subsequent references to that context will use the new path
   - Redefinition does not affect previously defined files/directories

1. Path Resolution:
   - When using a context, the path provided is appended to the context path
   - The resulting path must be normalized (no `//`, `..`, or `.` references)
   - Paths used with contexts must not begin with a forward slash

### Examples

```hra
# Create a context using '^' as modifier
=^ /contains/the/below/

# Use the '^' context
=^ README.md  # resolves to /contains/the/below/README.md

# Create another context using '$' as modifier
=$ /different/path/
=$ file.txt   # resolves to /different/path/file.txt

# Create nested context using '@' as modifier
=^ /root/project/
=^@ src/      # creates '@' context at /root/project/src/
=@ main.c     # resolves to /root/project/src/main.c

# Redefine an existing context
=^ /new/root/path/     # redefines '^' context
=^ file.txt            # now resolves to /new/root/path/file.txt

# Previous files defined under old context remain unchanged
# but new files use the redefined context
```

### Creating Nested Contexts

Contexts can be created relative to other contexts by combining modifiers:
- First modifier references the existing context
- Second modifier creates the new context
- Both modifiers can be any valid characters

Example:
```hra
=^ /base/path/         # Create primary context
=^@ source/           # Create '@' context under '^' -> /base/path/source/
=^# tests/            # Create '#' context under '^' -> /base/path/tests/
=@ code.c             # Uses '@' context -> /base/path/source/code.c
=# test.c             # Uses '#' context -> /base/path/tests/test.c
```

### Invalid Usage Examples

```hra
# Invalid - relative path without context
= relative/path.txt

# Invalid - context path starting with slash when using context
=^ /root/dir/
=^ /other.txt        # Invalid - cannot use absolute path with context

# Invalid - attempting to navigate above context
=^ /root/dir/
=^ ../file.txt      # Invalid - tries to go above context

# Invalid - undefined context
=@ file.txt         # Invalid - '@' context not yet defined
```

## Attribute Inheritance Chain

The HRA format uses a sequential inheritance chain to determine file and directory attributes, encodings, and formats when not explicitly specified.
First, any explicitly stated attributes take precedence.
If attributes are missing, the system traverses up the directory tree from the immediate parent to the root directory, using the first encountered set of attributes.
Finally, if no attributes can be inherited through these means, the system falls back to default values.

## Encoded Data

Encoded data in the HRA format represents how the content is stored within the archive itself, not the original file's encoding.
When data is extracted from the archive, the implementation translates it back to its original form.
This is similar to how base64 is used in email attachments - the actual file isn't base64 encoded, but its storage/transmission format is.

An encoding is specified with the `encoding` operator.

Do not confuse a file that actually contains encoded data with the archived encoded data. If the file contains encoded data, for example base64, then an encoding should not be specified.

Encoding inheritance follows the same logic as the [Attribute Inheritance Chain](#attribute-inheritance-chain).

Common encodings:
- `base64` - Common binary encoding.
- `base32` - Common binary encoding.
- `hex`    - Common binary encoding.
- `escape` - Custom text encoding called [Escaped Encoding](#escaped-encoding).
- `ascii`  - Common text encoding.
- `utf8`   - Common text encoding. The default encoding. Only required implementation.

### Storing Binary Data

Raw binary data cannot be stored in the file since it will be mangled by text translations.
To store binary data it must be encoded.
Only one encoding can be applied.

Examples
```hra
# a base64 encoded file that needs to be translated before it can be used.
= binary.dat $base64
SGVsbG8gV29ybGQ=

= compressed.bin $base64
KLUv/QRYWQAAaGVsbG8Ed29ybGQCAXf6qXgA
```

### Escaped Encoding

Escaped encoding is a customized UTF-8 encoding for this archive that allows for:
- common control characters to be represented with printable characters
- all characters to be specified with an hex value

The backslash `\` is known as the escape character, and when combined with another character it forms an escape sequence.

The escaped characters will be translated to their actual values. The supported escapes are
  - `\0` - Null character (0x00)
  - `\a` - Bell/Alert (0x07)
  - `\b` - Backspace (0x08)
  - `\t` - Tab (0x09)
  - `\n` - Newline (0x0A)
  - `\v` - Vertical tab (0x0B)
  - `\f` - Form feed (0x0C)
  - `\r` - Carriage return (0x0D)
  - `\\` - Backslash (0x5C)
  - `\xhh` - Hexadecimal escape (where hh is any hex value)

Actual newlines that end data lines are included in the data.
This means newlines can be specified with `\n` and an actual newline,
so it is unnecessary to end lines with a `\n` to include newlines in the data.

## Formatted Data

The `format` operator is optionally defined.

## Text

For text files stored in UTF-8 within the archive, the format operators specifies what text encoding and line endings to use when exporting the file data.
Only one text encoding and one line ending specification can be active for a given file.
If the format operator is undefined in the header line, all text content is stored and extracted as UTF-8 with the original line endings preserved; text formatting specifications cannot be used since the operator is not defined.

Each format specification consists of:
1. A single space after the path and any encodings (or previous format specifications)
2. The format operator
3. The specification name, optionally bracketed

Text Encodings
- `utf8` - UTF-8 encoding
- `utf16le` - UTF-16 little endian
- `utf16be` - UTF-16 big endian
- `utf32le` - UTF-32 little endian
- `utf32be` - UTF-32 big endian
- `ascii` - ASCII encoding
- `latin1` - ISO-8859-1 encoding

Line Endings
- `lf` - Unix style (LF)
- `crlf` - Windows style (CRLF)
- `cr` - Classic Mac style (CR)

Examples
```hra
# Set Windows line endings for all files
= / @crlf

# UTF-16LE file in Windows directory
= /windows/config.txt @utf16le
Hello World

# Override parent directory line endings
= /unix/ @lf
= /unix/script.txt @utf16le  # Uses LF
Text content

# Multiple formatting specs inherited from different levels
= /docs/ @utf16le
= /docs/windows/ @crlf
= /docs/windows/readme.txt    # Uses utf16le and crlf

# No formatting specifications needed for UTF-8 content
= /notes.txt
Plain UTF-8 text
```

### Binary

Binary files stored with encodings like base64.

They do not use a format specifications, but to prevent inheriting text format specifications, an empty format specification can be used. For example:
```hra
# Set defaults for directory
= / @utf16le @crlf
# Inherits both utf16le and crlf
= /text.txt
# Empty decode prevents inheritance
= /data.bin $base64 @
SGVsbG8gV29ybGQ=
```

## Attribute Standards

The fourth header line can optionally include `standard=` to specify the attribute standard being used.
Attributes not included in the standards are not in error and are allowed.

No implementation is required to support any standard, but implementations may:
- Ignore unsupported attribute standards  
- Provide best-effort attribute mapping
- Warn about unsupported or missing standards

### File System

#### Time (`standard=time`)

Values are formatted in ISO 8601 with required timezone

Attributes
- `created`: Creation timestamp
- `modified`: Content modification timestamp
- `accessed`: Last access timestamp
- `changed`: Metadata change timestamp

Example
```hra
= /etc/config.txt modified=2024-11-12T15:30:00Z
```

#### POSIX (`standard=posix`)

Core POSIX attributes that any POSIX-compliant filesystem must support.

Attributes:
- `posix_owner`: POSIX username
- `posix_group`: POSIX group name
- `posix_mode`: POSIX-style permission string (e.g. `rwxr-xr-x`)
- `posix_acl`: POSIX.1e ACL format

Example:
```hra
= /etc/config.txt \
    posix_mode=rw-r----- \
    posix_owner=admin \
    posix_group=security
```

#### Single Unix Specification, Version 4 (`standard=susv4`)

Unix features.

Attributes:
- `susv4_xattr`: Extended attributes (`name=value` pairs)
- `susv4_caps`: POSIX capabilities text format

Example:
```hra
= /etc/config.txt susv4_xattr=security.selinux=system_u:object_r:config_t:s0
```

#### HFS+ (`standard=hfs+`)

HFS+ specific features.

Attributes:
- `hfs_flags`: HFS-specific file flags (hex)
- `hfs_xattr`: HFS-specific extended attributes
- `hfs_rsrc`: Resource fork data (encoded)
- `hfs_type`: File type code (4 chars)
- `hfs_creator`: Creator code (4 chars)

Example:
```hra
standard=hfs+
= /Applications/App.app/Contents/Info.plist \
    hfs_flags=0x00000040 \
    hfs_type=TEXT \
    hfs_creator=MSFT
```

#### NTFS 3.1 (standard=ntfs3.1)

Attributes
- ntfs_owner: Windows account name/SID
- ntfs_attrs: Windows attributes string (e.g. RHSA)
- ntfs_acl: Windows SDDL format
- ntfs_ads: Alternate data streams (name:size pairs)
- ntfs_reparse: Reparse point data (hex)
- ntfs_compressed: NTFS compression (true/false)
- ntfs_encrypted: NTFS encryption (true/false)
- ntfs_sparse: Sparse file (true/false)

Example:
```hra
= /Config/App/settings.ini \
    ntfs_attrs=RH \
    ntfs_owner=SYSTEM \
    ntfs_acl=D:PAI(A;;FA;;;SY)(A;;FA;;;BA)
```

#### FAT32 (`standard=fat32`)

Attributes
- `fat_name`: 8.3 filename (short name)
- `fat_attrs`: DOS attribute flags (string containing any of):
  - `R`: Read-only
  - `H`: Hidden
  - `S`: System
  - `A`: Archive
  - `D`: Directory
  - `V`: Volume label
- `fat_case`: Case information:
  - `lower_base`: Lowercase base name
  - `lower_ext`: Lowercase extension
  - `mixed`: Mixed case preserved
- `fat_lfn`: Long filename (if different from path name)

Example:
```hra
standard=time,fat32
= /DOCUMENT/REPORT.TXT \
    fat_name=REPORT.TXT \
    fat_attrs=A \
    fat_case=lower_base,lower_ext \
    fat_lfn=Quarterly Report.txt \
    created=2024-11-12T15:30:00Z \
    accessed=2024-11-12 \
    modified=2024-11-12T15:30:00Z
```

Implementation Notes
1. FAT32 Specific Considerations:
  - Time resolution is limited (2-second for modify time, date-only for access)
  - Case preservation depends on filesystem implementation
  - 8.3 names must be stored for compatibility
  - Directory entries may have both short and long names
1. Standard Limitations:
  - FAT32 doesn't support ownership
  - Access time is date-only
  - No permission bits or ACLs
  - Limited attribute set compared to modern filesystems

Multi-Standard Example
```hra
standard=posix,hfs+,ntfs3.1

= /shared/config.txt \
    posix_mode=rw-r----- \
    posix_owner=admin \
    posix_group=wheel \
    hfs_flags=0x00000040 \
    ntfs_attrs=RH \
    ntfs_owner=Administrator \
    modified=2024-11-12T15:30:00Z \
    ntfs_acl=D:PAI(A;;FA;;;SY)(A;;FA;;;BA)
```

#### ext4 (`standard=ext4`)

Attributes
- `ext4_flags` File and Directory Flags specified with a comma separated list of:
  - `sync`: Synchronous updates
  - `immutable`: Cannot be modified
  - `append`: Write only appends
  - `nodump`: Do not dump
  - `noatime`: Do not update access time
  - `dirsync`: Synchronous directory updates
  - `top`: Top of directory hierarchy
  - `project`: Project hierarchy
  - `encrypt`: Directory encryption
  - `casefold`: Case-insensitive directory
- `ext4_project`: Project ID for quota management
- `ext4_ea`: Extended attributes using ext4's format
  - Reserved namespaces: 
    - `security.*`
    - `trusted.*`
    - `system.*`
    - `user.*`
- `ext4_acl`: POSIX ACLs with ext4 extensions

Example:
```hra
= /data/encrypted/ \
    ext4_flags=encrypt,casefold \
    ext4_project=42 \
    ext4_ea=security.selinux=system_u:object_r:data_t:s0,security.ima=0123456789abcdef \

= /data/encrypted/important.txt \
    ext4_flags=sync,immutable \
```

#### UFS2/FFS (`standard=ufs2`)

Attributes
-`ufs_flags` File Fl
File Flags (`ufs_flags` as hex or comma-separated list):
  - `arch`: Archive needed
  - `nodump`: Do not dump
  - `opaque`: Directory is opaque for union mounts
  - `sappnd`: Secure append only
  - `schg`: Secure change only
  - `sunlnk`: Secure unlink only
  - `uappnd`: User append only
  - `uchg`: User change only
  - `uhidden`: Hidden file
  - `uunlnk`: User unlink only
- `ufs_ea`: Extended attributes in UFS2 format

Example:
```hra
standard=ufs2
= /var/db/secure/ \
    posix_mode=rwxr-x--- \
    posix_owner=root \
    posix_group=wheel \
    ufs_flags=sappnd,schg \
    ufs_ea=security.mac_biba=high \
    modified=2024-11-12T15:30:00Z
```

#### CBM DOS (`standard=cbmdos`)

File Types (`cbm_type` as two-character code):
- `DEL`: Deleted (scratched)
- `SEQ`: Sequential data file
- `PRG`: Program file
- `USR`: User file
- `REL`: Relative file (random access)

File Attributes:
- `cbm_name`: Original PETSCII filename (16 chars max)
- `cbm_record`: Record length for REL files (1-254 bytes)
- `cbm_locked`: File locked flag (true/false)

Example:
```hra
standard=cbmdos
= /games/GAME.PRG \
    cbm_type=PRG \
    cbm_name=GAME

= /data/SCORES.REL \
    cbm_type=REL \
    cbm_name=SCORES \
    cbm_record=30
```

Multi-Standard Example
```hra
standard=time,cbmdos,fat32
= /games/c64/GAME.PRG \
    cbm_type=PRG \
    cbm_name=GAME \
    fat_attrs=R \
    fat_name=GAME.PRG \
    modified=1984-12-24T12:00:00Z
```

Implementation Notes
1. File Type Handling:
   - `cbm_type` affects how data should be loaded/used
   - Programs (PRG) start with load address
   - REL files need record length for structure
   - SEQ files are straight data streams

2. Character Set Considerations:
   - PETSCII stored in `cbm_name` needs encoding preservation
   - Path names use normal filesystem encoding
   - Implementation must handle PETSCII-UTF8 conversion

3. Mapping Recommendations:
   - Use standard read-only flags instead of `cbm_locked`
   - Derive sizes from actual file content
   - Ignore physical layout attributes

4. Format Preservation:
   - Store file type for proper handling
   - Preserve PETSCII names for authenticity
   - Maintain REL file structure

#### Atari DOS (`standard=ataridosii`)

Attributes:
- `atari_type` File Type as hex or short name:
  - `00` or `non`: Normal (no special load address)
  - `01` or `bas`: BASIC program
  - `02` or `bin`: Binary load file
  - `03` or `lst`: BASIC listed file
  - `04` or `txt`: Text file (AtariWriter)
  - `05` or `dat`: Data file
  - `06` or `com`: Binary compiled (COM file)
  - `07` or `sav`: BASIC saved file
  - `20` - `7F`: Reserved by Atari
  - `80` - `FF`: User definable
- `atari_name`: Original filename (8 chars max, ATASCII)
- `atari_locked`: File locked flag (`true`/`false`)
- `atari_load`: Load address for binary files
- `atari_init`: Init address for binary files (optional)
- `atari_run`: Run address for binary files (optional)

Example:
```hra
= /games/INVADERS \
    atari_type=binary \
    atari_name=INVADERS \
    atari_locked=true \
    atari_load=16384      # Using decimal for human readability
    atari_init=16384
    atari_run=16384

= /docs/README \
    atari_type=text \
    atari_name=README \
    atari_locked=false

= /games/ADVENTURE \
    atari_type=basic \
    atari_name=ADVENTURE \
    atari_locked=true
```

Multi-Standard Example
```hra
standard=time,ataridosii,fat32

= /games/atari/INVADERS \
    atari_type=binary \
    atari_name=INVADERS \
    atari_locked=true \
    atari_load=16384 \
    fat_name=INVADERS.BIN \
    fat_attrs=R \
    modified=1982-06-15T12:00:00Z
```

Implementation Notes
1. Type Handling:
   - File type determines required attributes
   - Binary files need load address
   - Init/run addresses optional based on program needs
1. Character Set:
   - ATASCII stored in `atari_name` needs encoding preservation
   - Path names use normal filesystem encoding
   - Implementation must handle ATASCII-UTF8 conversion
1. Format Preservation:
   - Store minimal required metadata
   - Preserve original filenames
   - Maintain program load information
1. Protection Status:
   - Locked status affects write permission
   - May map to modern read-only equivalents
   - Should be preserved across extractions


### Future Standard Considerations

The following filesystem standards warrant future definition due to their historical significance or modern relevance. Each would require careful analysis to identify preservation-worthy attributes while excluding filesystem-internal details.

#### Modern Systems

##### ZFS (`standard=zfs`)
Advanced filesystem with rich metadata features.
Implementation details reserved for future specification.

##### Btrfs (`standard=btrfs`)
Linux copy-on-write filesystem with:
- Subvolume support
- Advanced RAID features
- Snapshot capabilities
- Compression options
- Checksumming
Implementation details reserved for future specification.

##### APFS (`standard=apfs`)
Apple's modern filesystem with:
- Container and volume concepts
- Encryption integration
- Snapshot support
- Space sharing
- Clone files
Implementation details reserved for future specification.

##### ReFS (`standard=refs`)
Microsoft's Resilient File System with:
- Integrity streams
- Block cloning
- Data deduplication
- Storage tiering
Implementation details reserved for future specification.

##### XFS (`standard=xfs`)
High-performance filesystem with:
- Real-time device support
- Guaranteed rate I/O
- Project quota system
- Rich attribute system
Implementation details reserved for future specification.

##### Object Storage (`standard=object`)
Modern cloud storage paradigm with:
- Rich metadata support
- Custom attributes
- Storage classes
- Retention policies
- Legal holds
Implementation details reserved for future specification.

##### GlusterFS (`standard=glusterfs`)
Distributed filesystem with:
- Volume types
- Replica configurations
- Geographic replication
- Custom extended attributes
Implementation details reserved for future specification.

#### Historical Systems

##### Apple DOS 3.3 (`standard=appledos33`)
Significant for Apple II system preservation. Implementation details reserved for future specification.

##### Apple ProDOS (`standard=prodos`)
Advanced Apple II filesystem with features influencing modern Apple systems. Implementation details reserved for future specification.

##### MSX-DOS (`standard=msxdos`)
Important for MSX system preservation and CP/M compatibility. Implementation details reserved for future specification.

##### ZX Spectrum +3DOS (`standard=plus3dos`)
Essential for ZX Spectrum software preservation. Implementation details reserved for future specification.

##### TRS-80 TRSDOS (`standard=trsdos`)
Significant for TRS-80 system preservation. Implementation details reserved for future specification.

##### CP/M (`standard=cpm`)
Historically significant early operating system filesystem. Implementation details reserved for future specification.

#### Implementation Considerations

When implementing support for any filesystem standard:

1. Focus on Essential Features
   - Preserve unique system characteristics
   - Use logical rather than physical attributes
   - Support extended metadata models
   - Exclude filesystem-internal mechanisms
1. Maintain Portability
   - Support cross-platform compatibility
   - Consider cloud and distributed scenarios
   - Enable format migrations
   - Handle character encoding properly
1. Address Security
   - Handle encryption requirements
   - Preserve access controls
   - Support compliance requirements
   - Maintain audit capabilities
1. Ensure Readability
   - Keep attribute names clear
   - Use human-readable values
   - Document format requirements
   - Provide clear examples
1. Support Integration
   - Map attributes between standards
   - Handle standard-specific encodings
   - Support metadata inheritance
   - Enable attribute translation

Each standard's specification should emphasize attributes that:
- Affect file content interpretation
- Preserve system-specific metadata
- Enable authentic reproduction
- Support historical accuracy
- Maintain file organization structures
- Address modern storage paradigms

### Archive Validation

Files can include checksums for validation:

Example
```txt
checksum=sha256=8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
```

Attribute Grammar
```ebnf
checksum-attribute = [opener] "checksum" [closer] assignment [opener] [checksum-type] [closer] assignment [opener] checksum [closer]
checksum = {"0".."9" | "a".."f"}
```

## Duplicate Paths

The HRA format specification requires path uniqueness.
Path uniqueness should be verified during a post-parse integrity check.

When duplicate paths are detected during validation:
1. The validator should report:
  - The duplicate path
  - The line numbers of all occurrences
  - Whether the duplicates have identical or different content/attributes
1. Suggested recovery methods:
  a. If the CST for the paths are the same, keep the first occurrence (recommended for automated recovery)
  a. Rename duplicates with a suffix (e.g. file_0.txt, file_1.txt, ...)

## Preprocessors

The HRA format uses preprocessor verification through removal markers.
Each preprocessor listed in the file must remove its specific marker for the file to be valid.
If any markers remain, the HRA parser rejects the file as incompletely processed.

This provides two key benefits:
1. Guaranteed preprocessing completion - unprocessed files are invalid
1. Enforced preprocessing order - markers must be removed in sequence since earlier preprocessors will fail if they find markers that should come after them

The verification is achieved by embedding markers before the header that preprocessors must remove. Each marker:
- Has a unique random suffix to prevent collisions
- Names its required preprocessor
- Must be removed by its preprocessor for the file to be valid
- Must appear before the "Human" string that starts the header

Each preprocessor needs to:
1. Verify no markers that should come after it are present
1. Find and remove its specific marker
1. Leave all subsequent preprocessor markers unchanged

### Key Implementation Notes

1. **Error Handling**
   - Preprocessors should verify that no markers that should come after them exist
   - Should fail if incorrect order detected or own marker not found

1. **Marker Uniqueness**
   - Use sufficiently random strings (e.g. 8+ hex chars)
   - Include preprocessor name to avoid confusion
   - Consider including version info if relevant

1. **Line Preservation**
   - Most preprocessors should preserve line numbers
   - Important for error reporting
   - Some may need wrapper scripts to ensure this

1. **Header Validation**
   - By placing markers before the "Human" string, any unprocessed markers will prevent the file from being recognized as a valid HRA file
   - This provides immediate validation failure rather than requiring a full file parse

1. **Processing Order**
   - Order is enforced simply by the sequence of marker lines
   - Each preprocessor must verify no markers that should come after it are present
   - Makes it impossible to run preprocessors out of order without adding complexity to the markers themselves

### Examples

Here's a sequence of preprocessors that must run in order: template  cpp  sed

1. **Initial file state**
```hra
REQUIRES_TEMPLATE_PREPROCESSING_v84m3c62
REQUIRES_CPP_PREPROCESSING_w51t8x26
REQUIRES_SED_PREPROCESSING_x394nv84
Human Readable
Archive
0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
```

**template processor**
```python
def process_template(infile, outfile):
    with open(infile) as f:
        first_line = f.readline().strip()
        if first_line != 'REQUIRES_TEMPLATE_PREPROCESSING_v84m3c62':
            raise ValueError("Template must be first preprocessor")
            
        with open(outfile, 'w') as out:
            for line in f:
                out.write(process_line(line))
```

1. **After template processing**
```hra
REQUIRES_CPP_PREPROCESSING_w51t8x26
REQUIRES_SED_PREPROCESSING_x394nv84
Human Readable
Archive
0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
```

**cpp processor**
```bash
# function can be used to wrap other preprocessors
run_if_first() {
    local marker="$1"
    local cmd="$2"
    shift 2
    
    if [ "$(head -n1 input.hra)" = "$marker" ]; then
        "$cmd" "$@"
    else
        echo "Error: Expected $marker as first line" >&2
        exit 1
    fi
}

run_if_first "REQUIRES_CPP_PREPROCESSING_w51t8x26" cpp -DREQUIRES_CPP_PREPROCESSING_w51t8x26 input.hra output.hra
```

1. **After cpp processing**
```hra
REQUIRES_SED_PREPROCESSING_x394nv84
Human Readable
Archive
0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
```

**sed processor**
```bash
marker="REQUIRES_SED_PREPROCESSING_x394nv84"
sed "1{/^$marker\$/!q1};/$marker/d" input.hra > output.hra
```

1. **After sed processing (final state)**
```hra
Human Readable
Archive
0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
```

## Security Considerations

Only YOU can prevent archive vulnerabilities! 

Key security considerations:
1. Always validate paths for directory traversal attempts
2. Verify file permissions before extraction
3. Check for symlink attacks
4. Validate all attribute values
5. Implement size limits appropriate for your use case
6. Scan content for malware before extraction
7. Verify checksums when provided
8. Consider the implications of encoded content
9. Validate all UTF-8 sequences

# Sparse Files (DRAFT PROPOSAL)

> **Note**: This section is a draft proposal for handling sparse files in the HRA format. It is not yet part of the official specification and is provided for discussion purposes only. Implementations should not rely on these attributes until they are formally adopted into the specification.

Sparse files, which contain sequences of zero bytes that aren't physically stored on disk, present unique challenges for archival. This proposal outlines a standard for preserving sparse file information and efficient storage.

Implementation Notes:
1. Extraction Behavior:
   - Implementations should create sparse files when supported by the target filesystem
   - On filesystems without sparse file support, zero regions must be fully written
   - Block size alignment should be preserved where possible

2. Storage Considerations:
   - Only non-zero regions need to be stored in the archive
   - The sparse map provides reconstruction information
   - Tools should verify sparse_map entries align with sparse_blocks

3. Validation Requirements:
   - All offsets and lengths must be multiples of sparse_blocks
   - sparse_map regions must not overlap
   - sparse_map offsets must be in ascending order
   - Total mapped size must not exceed sparse_size

# Case Sensitivity in Paths (DRAFT PROPOSAL)

This section is completely computer generated and unreviewed.
I only plan on working on this if it is actually problematic.

> **Note**: This section is a draft proposal for ensuring compatibility, reproducibility, and ambiguity of case sensitive and insensitive file systems in the HRA format. It is not yet part of the official specification and is provided for discussion purposes only. Implementations should not rely on these attributes until they are formally adopted into the specification.

## Abstract

This proposal defines standard behavior for handling case sensitivity in file and directory paths within Human Readable Archive (HRA) files. It addresses the challenges of cross-platform compatibility between case-sensitive and case-insensitive filesystems.

## Background

File systems differ in their handling of case sensitivity:
- Case-sensitive: Treats `file.txt` and `FILE.txt` as distinct files (e.g., Linux ext4)
- Case-insensitive: Treats `file.txt` and `FILE.txt` as the same file (e.g., Windows NTFS)
- Case-preserving: Maintains case in filenames but treats different cases as same file

This variance creates challenges when:
1. Creating archives on one system type for use on another
2. Validating archive integrity across platforms
3. Extracting archives to different filesystem types

## Proposal

#### 1. Path Normalization

##### 1.1 Canonical Form

All paths in HRA files MUST be normalized to a canonical form before uniqueness validation:

```
normalized_path = unicode_normalize(remove_duplicate_separators(absolute_path))
```

Where:
- `unicode_normalize`: Applies Unicode NFKC normalization
- `remove_duplicate_separators`: Replaces multiple '/' with single '/'
- `absolute_path`: Full path starting with '/'

##### 1.2 Case Comparison Form

For case-insensitive comparison, paths MUST be converted to a case-folded form:

```
case_folded_path = unicode_case_fold(normalized_path)
```

#### 2. Validation Requirements

##### 2.1 Strict Validation

Implementations MUST:
1. Detect paths that differ only by case
2. Identify the target system's case sensitivity
3. Report conflicts during validation phase

##### 2.2 Validation Modes

Implementations MUST support these validation modes:

1. **Strict Mode** (Default)
   - Reject archives with case conflicts
   - Return error code `ECASE_CONFLICT`
   - List all conflicting paths

2. **Warning Mode**
   - Allow case conflicts
   - Log detailed warnings
   - Continue processing

3. **Auto-resolve Mode**
   - Automatically rename conflicting files
   - Document changes in metadata
   - Continue processing

#### 3. Conflict Resolution

##### 3.1 Resolution Strategies

When operating in Auto-resolve Mode, implementations MUST support these strategies:

1. **Suffix Strategy** (Default)
   ```
   file.txt  file.txt
   FILE.txt  file_1.txt
   File.txt  file_2.txt
   ```

2. **Preserve Strategy**
   - Create platform-specific escape sequences
   - Document in metadata
   ```
   file.txt   file.txt
   FILE.txt   file{UPPER}.txt
   File.txt   file{Title}.txt
   ```

3. **Last-Wins Strategy**
   - Keep only the last occurrence
   - Log skipped entries

##### 3.2 Resolution Metadata

Implementations MUST record case resolutions in metadata:

```hra
= /.hra-meta/case-resolution.txt
original_path=FILE.txt
resolved_path=file_1.txt
resolution_strategy=suffix
timestamp=2024-11-14T12:00:00Z
```

#### 4. Implementation Requirements

##### 4.1 Required Capabilities

Implementations MUST:
1. Support all validation modes
2. Support all resolution strategies
3. Provide configuration options
4. Maintain resolution logs
5. Document their default behavior

##### 4.2 Configuration Options

Minimum required options:
```
--case-sensitivity=(strict|warning|auto)
--resolution-strategy=(suffix|preserve|last-wins)
--case-aware=(true|false|auto)
```

##### 4.3 Error Handling

Standard error codes:
```
ECASE_CONFLICT    = 100  // Case conflict detected
ECASE_RESOLVE     = 101  // Resolution failure
ECASE_UNSUPPORTED = 102  // Case handling not supported
```

#### 5. Examples

##### 5.1 Conflict Detection
```hra
= /docs/readme.txt
This is version 1

= /docs/README.txt
This is version 2

// Validation output:
Error: Case conflict detected
  Path 1: /docs/readme.txt (line 1)
  Path 2: /docs/README.txt (line 4)
```

##### 5.2 Auto-resolution
```hra
= /docs/readme.txt
Original content

= /docs/README.txt
Different content

// Resolution output (with suffix strategy):
Created: /docs/readme.txt
Created: /docs/readme_1.txt
Resolution logged to /.hra-meta/case-resolution.txt
```

##### 5.3 Platform-specific Metadata
```hra
= /.hra-meta/platform.txt
case_sensitive=false
filesystem=NTFS
resolution_strategy=suffix
```

## Backwards Compatibility

This proposal maintains backwards compatibility:
1. Existing archives remain valid
2. New behavior only triggers on case conflicts
3. Default strict mode preserves current expectations
4. Metadata additions are optional

## Security Considerations

Implementations MUST:
1. Prevent path traversal via case variants
2. Validate all generated filenames
3. Limit automatic rename iterations
4. Verify resolution metadata integrity
5. Sanitize logged paths

## Future Considerations

1. Extended attributes for case preferences
2. Unicode normalization options
3. Custom resolution strategies
4. Network filesystem handling
5. Cloud storage compatibility

## References

1. POSIX filename specifications
2. Windows naming conventions
3. Unicode Technical Standard #10
4. Filesystem case sensitivity documentation

# Timezone Configuration (DRAFT PROPOSAL)
Version: 0.1

**NOTICE: This is an unreviewed, AI-generated draft proposal that requires thorough error checking and validation before consideration.**

## Overview
Add timezone configuration support to the HRA format to provide timezone information for the archive content.

## Motivation
Timezone information is fundamental to timestamp representation. Including it in the archive configuration allows:
1. Simplified timestamp representation in attributes by making the timezone implicit
2. Clear documentation of the temporal context for archive content
3. Single source of truth for timezone interpretation

## Specification

### Header Line Addition
Add optional `timezone=` to the archive configuration line (line 4). The value must be an ISO 8601 timezone offset.

#### Syntax
```
timezone={+|-}HHMM|Z
```
Where:
- `{+|-}`: Mandatory plus or minus sign (except for Z)
- `HH`: Hours (00-23)
- `MM`: Minutes (00-59) 
- `Z`: Special case indicating UTC (Zulu time)

#### Examples
```hra
Human Readable
Archive
0.1
meta= comment# timezone=+0500
```

```hra
Human Readable
Archive
0.1
meta= comment# timezone=-0800 escape\ opener"
```

```hra
Human Readable
Archive
0.1
meta= comment# timezone=Z
```

### Timestamp Formats
With timezone defined in the archive header, attribute timestamps may be specified in two formats:

1. Full ISO 8601 with explicit timezone (existing format):
```hra
modified=2024-01-15T14:30:00-0500
```

2. Implicit timezone format (new option):
```hra
modified=2024-01-15T14:30:00
```
Which inherits timezone from archive configuration.

### Behavior
1. Timestamps with explicit timezone information use their specified timezone
2. Timestamps without timezone information use the archive's configured timezone
3. If no timezone is configured in the archive:
   - Implementations MUST assume UTC
   - Implementations MUST require explicit timezone in all timestamps

### Validation
1. The timezone value MUST match the ISO 8601 timezone offset format:
   - `Z` or
   - `+` or `-` followed by exactly 4 digits representing a valid hour (00-23) and minute (00-59)
2. Invalid timezone values MUST cause validation failure

### Migration
1. Existing archives without timezone configuration implicitly default to UTC
2. Archive processors MUST require explicit timezone in timestamps when processing archives without timezone configuration

## Rationale
1. ISO 8601 timezone offset format chosen because:
   - Matches timestamp format already used in the specification
   - Universally supported by ISO 8601 implementations
   - No external dependencies like IANA timezone database
   - Human readable
   - Compact representation

2. UTC default chosen because:
   - Most widely used timezone for data interchange
   - Zero offset simplifies implementation
   - Matches common practice

## Implementation Notes
1. Implementations MUST validate timezone format
2. Implementations MUST validate all timestamps conform to either format
3. Implementations SHOULD optimize timestamp storage when using implicit format

## Examples

### Mixed Format Example
```hra
Human Readable
Archive
0.1
meta= comment# timezone=+0900
= /explicit.txt modified=2024-01-15T14:30:00Z      # Uses explicit UTC
= /implicit.txt modified=2024-01-15T14:30:00       # Uses archive timezone
```

## Discussion Points
1. Should we allow timezone changes within an archive via redefinition?
2. Should we require all timestamps in an archive to use the same format?
3. Should we add format validation flags for consistency enforcement?

## Alternatives Considered
1. IANA timezone names
   - Rejected due to unnecessary dependency
2. Requiring all timestamps use archive timezone
   - Rejected for compatibility with existing files
3. Multiple timezone formats
   - Rejected for complexity vs benefit

## Future Considerations
1. Timezone-specific metadata storage
2. Format validation flags
3. Redefinition rules

# File Extensions and Compression (DRAFT PROPOSAL)
Version: 0.1

**NOTICE: This is a draft proposal for standardizing file extensions and compression handling for the HRA format.**

## Overview
Define standard file extensions for Human Readable Archive files and establish conventions for handling external compression.

## Primary Extension
The canonical file extension for uncompressed Human Readable Archive files is:
```
.hra
```

## Compressed Archives
While the HRA format itself does not support internal compression to maintain human readability, it is designed to work well with external compression tools. For compressed HRA files, use these standard extensions:

### Unix-style Extensions
Standard compression extensions follow the pattern `.hra.{compression}`

#### Primary Formats (Widely Supported)
- `.hra.gz`  - gzip (GNU Zip)
- `.hra.bz2` - bzip2 compression
- `.hra.xz`  - xz (LZMA2)
- `.hra.zst` - Zstandard compression
- `.hra.br`  - Brotli compression

#### Additional Formats
- `.hra.Z`    - compress (traditional Unix)
- `.hra.lz`   - lzip
- `.hra.lz4`  - LZ4
- `.hra.lzo`  - LZO
- `.hra.lrz`  - LRZIP
- `.hra.rz`   - rzip
- `.hra.lzma` - Legacy LZMA
- `.hra.zpaq` - ZPAQ
- `.hra.sz`   - SZIP
- `.hra.zz`   - ZZip

### Windows-style Extensions
Standard compression extensions follow the pattern `.hra{compression}`

#### Primary Formats
- `.hraz`   - ZIP (standard Windows zip)
- `.hra7z`  - 7-Zip
- `.hrary`  - RAR (proprietary)
- `.hracab` - Microsoft Cabinet

#### Additional Formats
- `.hraarj` - ARJ
- `.hraarc` - ARC
- `.hralha` - LHA/LZH
- `.hraace` - ACE (proprietary)
- `.hrakgb` - KGB Archiver
- `.hrapaq` - PAQ series
- `.hrauha` - UHA
- `.hrawim` - Windows Imaging Format

### DOS-style Extensions
Standard compression extensions follow the pattern `.HR{compression}` (uppercase by convention)

#### Primary Formats
- `.HRZ` - PKZIP (DOS standard zip)
- `.HRA` - ARC (earliest DOS archiver)
- `.HRL` - LHA/LHARC (popular in DOS/Japan)
- `.HRJ` - ARJ (efficient DOS archiver)
- `.HR$` - SQZ (Squeeze)
- `.HRQ` - QEMM squeeze
- `.HRU` - UPS/File Express
- `.HRY` - Young Chang
- `.HR1` - `.HR9` - PAK series

#### Legacy Formats
- `.HRD` - DWC
- `.HRH` - HAP
- `.HRK` - KBoom
- `.HRM` - MDC
- `.HRN` - NOW
- `.HRP` - PGSC
- `.HRS` - BSC/BSA
- `.HRT` - TCP
- `.HRX` - eXpress
- `.HR#` - DIET (self-extracting)

## Format Detection

### Magic Numbers
- 7-Zip: `37 7A BC AF 27 1C`
- ACE: `2A 2A 41 43 45 2A 2A`
- ARJ: `60 EA`
- Brotli: `CE B2 CF 81`
- GZIP: `1F 8B`
- PKZIP/ZIP: 
  - Standard files: `50 4B 03 04`
  - Empty archive: `50 4B 05 06`
  - Spanned archive: `50 4B 07 08`
- RAR:
  - RAR4: `52 61 72 21 1A 07 00`
  - RAR5: `52 61 72 21 1A 07 01 00`
- bzip2: `42 5A 68`
- xz: `FD 37 7A 58 5A 00`
- Microsoft Cabinet: `4D 53 43 46 00 00 00 00`
- Windows Imaging Format: `4D 53 57 49 4D 00 00 00`

### File Headers/Signatures
- LHA/LZH: `-lh0-` to `-lh7-` header types
- LZMA: `5D 00 00` (raw LZMA)
- Zstandard: `28 B5 2F FD`

### Additional Format Identification
- ARC: First byte typically 0x1A
- BSA: Commonly found in Bethesda games
- LZ4: Frame descriptor at start
- LRZIP: LRZIP header structure
- PAQ series: Version number in filename
- ZPAQ: ZPAQ block headers
- rzip: Compression block structure
- UHA: Version identifier in header
- DIET: DOS executable with DIET signature

## Implementation Requirements

### Compression Detection
Implementations MUST:
1. Support reading uncompressed `.hra` files
2. Detect compressed formats through both:
   - File extension matching
   - Magic number verification when available
3. Provide clear error messages when compression support is not available

### Compression Support
Implementations SHOULD:
1. Support reading at least the primary formats for their platform
2. Document which compression formats are supported
3. Provide configuration options for compression preferences
4. Handle decompression transparently when supported

Implementations MAY:
1. Support writing compressed archives
2. Support additional compression formats
3. Provide compression level options
4. Cache decompressed content for performance

## Examples

### Command Line Usage
```bash
# Creating archives
hra create archive.hra
hra create - | gzip > archive.hra.gz

# Reading archives
hra read archive.hra
zcat archive.hra.gz | hra read -
```

### Implementation Example
```python
def detect_format(filename, header):
    """Detect HRA format and compression"""
    if header.startswith(b'Human'):
        return 'hra'
    elif header.startswith(b'\x1f\x8b'):
        return 'gzip'
    elif header.startswith(b'BZh'):
        return 'bzip2'
    # ... additional format checks ...
    
    raise UnsupportedFormatError(f"Unknown or unsupported format: {filename}")
```

## Security Considerations

Implementations MUST:
1. Validate magic numbers before decompression
2. Set reasonable decompression size limits
3. Handle decompression errors gracefully
4. Scan for compression bombs
5. Verify file integrity after decompression
6. Validate all file paths after decompression
7. Check for unusual compression ratios
8. Implement timeouts for decompression operations

## Future Considerations

1. Standard compression level recommendations
2. Streaming support guidelines
3. Multi-file archive handling
4. Additional compression format support
5. Content verification methods
6. Legacy format preservation guidelines
7. Platform-specific optimization recommendations

