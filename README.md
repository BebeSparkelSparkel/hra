# Human Readable Archive (hra)

A file that contains directory information, file contents, and meta-data.

Example:

```hra
Human Readable
Archive
0.1
meta= comment# redefine~ escape\ assignment= continuation\ opener" closer" encoding
# The first two lines are more expressive than they seem. They define the type of whitspace character and newline string used in the rest of the file.
# The third line holds the file format version.
# The forth line defines the:
# - required meta string prefix is assigned to `=`
# - optional comment string prefix is assigned to `#`
# - optional redefine string prefix is assigned to `~`
# - optional escape string prefix is assigned to `\`
# - optional attribute assignment string is assigned to `=`
# - optional line continuation string is assigned to `\`
# - optional opener for contiguous strings is assigned to `"`
# - doubly optional closer of contiguous strings is assignd to '"'
# - optional file encoding prefix is assigned to "$"

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
# This is a comment. The below escape character escapes the comment prefix so it is included in the file data.
/#!/bin/bash
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
; This is a comment. The above redefines the meta and comment prefix characters to be `@` and `;`
; It also undefines the redefine prefix which can longer be used.
; The comment prefix character can also be undefined in the same way.

@ /files/and/directories/are/now/defined/with/the/@/prefix.txt

; The below directory definition shows how attributes can be place on following lines using line ending back slash for readability.
; The leading spaces are optional.
; The meta defintion ends when the last line does not have an ending back slash.
@ /multiple/line/ \
    perm=rwxrwxrwx \
    atime=2024/11/4-10:47:44

; Multiple encodings (applied in order left to right)
@ /compressed.dat $zstd $base64
H4sIAAAAAAAAA0vLz1FIy8xLAQBuYJ0LDQAAAA==
```

## Format

### Header

The header is four lines and defines how to how to interpret the rest of the file.

#### First and Second Line

The first line must start with the string `Human`. Then:
1. One character to be used as the space character for the rest of the file
    - This documentation refers to this character as **space**
    - Typically, it is the *space* `0x20` or *tab* `0x09` character. Tab may be a good choice if your paths contain many `0x20` *spaces*.
1. The string `Readable`
1. The character string after `Readable` and before `Archive` defines the newline string for the rest of the document
1. The string `Archive`

#### Third (Version) Line

The third line holds the hra file format version (e.g. `0.1`) which contains

The line contains:
1. the major version digits (version numbers us base 10 digits)
1. a `.` separator
1. the minor version digits

View the (Format Version)[#format-version) section for the version meaning.

#### Fourth (Prefix Assignment) Line

The line must contain the string assignments:
- `meta`: Required. The string that indicates the line specifies a new file or directory is being defined. Must be assigned a value.
- `comment`: Optional. The string that indicates the line is a comment. Can be indicated without value if `redefine` is specified and assigned.
- `redefine`: Optional. The string that indicates the line specifies a prefix redefinition. If indicated, must be assigned a value in this line but may be undefined in later redefinitions.
- `escape`: Optional. The string that allows the prefixes to begin the line of a file data. Can be indicated without value if `redefine` is specified and assigned.
- `assignment`: Optional. The string that separates the attribute's name and value. Can be indicated without value if `redefine` is specified and assigned.
- `continuation`: Optional. The string that appears at the end of a line which indicates a line continuation. Can be indicated without value if `redefine` is specified and assigned.
- `opener`: Optional. The string that is prefixed to a contiguous string that only ends with the closer string. Can be indicated without value if `redefine` is specified and assigned.
- `closer`: Doubly Optional. If undefined, its value is the same as `opener`. It is also the only thing the escape prefix can escape in the contiguous string. Can be indicated without value if `redefine` is specified and assigned.
- `encoding`: Optional. The string that indicates content encoding for a file. If undefined, content encoding cannot be specified. Can be indicated without value if `redefine` is specified and assigned.

Parser Capability Requirements:
- All prefixes that will be used anywhere in the file MUST be indicated in this line for parser capability checks
- Any attempt to use or redefine a prefix that was not indicated in this line MUST result in a parse error

To define the prefixes:
1. The prefix name
1. No separating spaces
1. The prefix string. It may not contain spaces.
1. Optionally, to have more prefix assignments the following can be repeated:
    1. A single space
    1. More prefix assignments as described in the steps above.

For example, `meta= comment// escape123` defines the:
- Meta string to be `=`
- Comment string to be `//`
- Escape string to be `123`
- The other prefix strings to be undefined and unusable

### Content

#### Meta Line

The meta line defines a path for a file or directory and its attributes.

The meta prefix is defined with the `meta` prefix string assignment and must be defined.

The definition at a minimum requires the following in order on the same line:
1. The meta string
1. An optional directory context string, which contains no space
1. A single space
1. A POSIX path, optionally bracketed
    - If referencing an already defined directory context, the path must not begin with a forward slash
    - If not bracketed, spaces may not be used

If the POSIX path ends with trailing single forward slash, a directory is specified and not a file.

If the `encoding` prefix is defined, zero or more encodings can be specified after the path:
1. A single space
1. The encoding prefix string
1. The encoding name string, optionally bracketed
1. Optionally, steps 1-2 can be repeated for additional encodings which are applied left to right

Optionally (there are no required attributes), attributes can be associated with the definition with:
1. At least one leading spaces
1. Optionally, to allow multi-line attribute assignments the following the following can be included:
    1. The line continuation string
    1. Zero or more spaces
1. The attribute name string, optionally bracketed
1. The attribute assignment string
1. The attribute value string, optionally bracketed
1. Optionally, the above steps can be repeated for more attribute assignments

#### Data Lines

The file data need to be preceded file meta line.

The lines following the file meta line are data lines.
Data lines can be intermixed with lines that begin with comments and redefinitions that should not be included in the data.
To have the comment, redefinition, escape string at the beginning of a line, the escape string can precede the strings to allow them to be included in data.

The data ends at the last non-empty data, non-prefixed lines, before the next meta line or end of file.
Trialing newlines are left to the implementations and attributes. It is suggested to use the attribute field name of `enls` (Ending Newlines).

Warning! Be aware of newline translation because if you include mismatched newlines they may be changed upon file write.

#### Comments

Comments can only be started at the beginning of the line and continue to the end of the line.
The defined comment prefix must begin the line.
They can appear intermixed in data lines.
To define data lines that begin with the comment string, prefix the comment string with the escape string.

The comment prefix is defined with the `comment` prefix string assignment and is optional.
If undefined, comments cannot be used.

#### Redefintions

Prefixes can be redefined throughout the document.

The redefinition prefix is defined with the `redefine` prefix string assignment.
If undefined, redefinitions cannot be used.

Redefintions are indicated by a line starting with the redefinition string.
There can be multiple redefinitions in the same line.
Beyond redefining, 

The redefinition prefix is Claude this

## Character Encoding

The Human Readable Archive format uses UTF-8 character encoding without Byte Order Mark (BOM). The absence of BOM is required to ensure the file begins directly with "Human" as specified in the header requirements. All implementations must support UTF-8 encoding to ensure universal compatibility and proper handling of international characters.

## Bracketing

The HRA format supports bracketing of strings to allow the inclusion of spaces and other special characters. A bracketed string is one that begins with the `opener` string and ends with the `closer` string. If the `closer` string is undefined in the prefix assignments, the `opener` string is used as both the opening and closing bracket.

### Common Use Cases

Bracketing is particularly useful for:
- Paths containing spaces
- Paths containing special characters that might otherwise require escaping
- Paths containing the meta, comment, or redefine prefix strings
- Any string that needs to preserve its exact formatting, including whitespace

### Implementation and Behavior

Required behavior when a string is bracketed:
- Must be opened
- Spaces and other special characters are allowed within the string
- The string is treated as a contiguous unit
- The `closer` string can be escaped using the escape prefix to include it within the string
- No other characters need to be escaped within the bracketed string
- Once a string is opened, it must be closed before the end of the line

Suggested practices for bracketing:
- Choose bracketing strings that minimize conflicts with common path characters
- Use short strings (1-2 characters) for readability

### Examples

Given prefix assignments:
```
opener" closer" escape\    # Double quote as both opener and closer, backslash as escape
```

Valid bracketed paths:
```
= "Program Files/example.txt"     # Spaces allowed in path
= "My Documents/report.doc"       # Another space-containing path
= "Users/John\"Smith/file.txt"    # Escaped quote within path
```

Given prefix assignments with different opener and closer:
```
opener< closer> escape\      # Angle brackets as opener/closer, backslash as escape
```

Valid bracketed paths:
```
= <C:/Users/John Doe/file.txt>    # Spaces allowed in path
= <data files/raw data.csv>       # Multiple spaces handled
= <data\>files/example.txt>       # Escaped > within path
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

Directory context is a feature that allows for more concise file and directory definitions when working with deeply nested paths. It uses context modifiers with the meta prefix to establish and reference directory contexts, making it easier to organize related files.

### Context Modifiers

Context modifiers are single characters that can be appended to the meta prefix to establish or reference directory contexts. While the examples show `^`, `-`, and `*`, any character can be used as a context modifier. The creator of the archive can choose whatever characters make sense for their use case.

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
   - The resulting path must be normalized (no `..` or `.` references)
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

The HRA format uses a sequential inheritance chain to determine file and directory attributes when not explicitly specified.
First, any explicitly stated attributes take precedence.
If attributes are missing, the system traverses up the directory tree from the immediate parent to the root directory, using the first encountered set of attributes.
If no ancestor directories specify attributes, it examines child directories and uses the most commonly specified attributes among them (using the first child's attributes in case of ties).
Finally, if no attributes can be inherited through these means, the system falls back to default values: `rw-r--r--` and `root:root` for files, and `rwxr-xr-x` and `root:root` for directories.
Each attribute (permissions, user, group) is inherited independently, though certain attributes like symlink targets must always be explicitly specified.

## Encoded Data

Encoded data in the HRA format represents how the content is stored within the archive itself, not the original file's encoding. When data is extracted from the archive, the implementation decodes it back to its original form. This is similar to how base64 is used in email attachments - the actual file isn't base64 encoded, but its storage/transmission format is.

An encoding is specified with the `encoding` prefix.

Do not confuse a file that actually contains encoded data with the archived encoded data. If the file contains encoded data, for example base64, then an encoding should not be specified.

Common encodings:
- `base64` - Common binary encoding.
- `base32` - Common binary encoding.
- `hex`    - Common binary encoding.
- `escape` - Custom text encoding called (Escaped Encoding)[#escaped-encoding].
- `ascii`  - Common text encoding.
- `utf8`   - Common text encoding. The default encoding. Only required implementation.

### Storing Binary Data

Raw binary data cannot be stored in the file since it will be mangled by text translations.
To store binary data it must be encoded.
Only one encoding can be applied.

Examples
```hra
# a base64 encoded file that needs to be decoded before it can be used.
= binary.dat $base64
SGVsbG8gV29ybGQ=

= compressed.bin.zstd $base64
KLUv/QRYWQAAaGVsbG8Ed29ybGQCAXf6qXgA
```
### Escaped Encoding

Escaped encoding is a customized UTF-8 encoding for this archive that allows for:
- common control characters to be represented with printable characters
- all characters to be specified with an hex value

The backslash `\` is known as the escape character, and when combined with another character it forms an escape sequence.
Claude we need to figure out how this interacts with actual newline characters.

The escaped characters will be decoded to their actual values. The supported escapes are
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

## Attribute Standards

The fourth header line can optionally include `standard=` to specify the attribute standard being used. Common standards include:

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
= / standard=hfs+
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
```
= / standard=time,fat32
= /DOCUMENT/REPORT.TXT \
    fat_name=REPORT.TXT \
    fat_attrs=A \
    fat_case=lower_base,lower_ext \
    fat_lfn=Quarterly Report.txt \
    created=2024-11-12T15:30:00Z \
    accessed=2024-11-12 \
    modified=2024-11-12T15:30:00Z
```

##### Implementation Notes

FAT32 Specific Considerations:
- Time resolution is limited (2-second for modify time, date-only for access)
- Case preservation depends on filesystem implementation
- 8.3 names must be stored for compatibility
- Directory entries may have both short and long names

Standard Limitations:
- FAT32 doesn't support ownership
- Access time is date-only
- No permission bits or ACLs
- Limited attribute set compared to modern filesystems

#### Multi-Standard Example
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

#### Implementation Notes

1. POSIX Inheritance:
   - Standards implementing POSIX compliance inherit all `posix_*` attributes
   - POSIX attributes are interpreted according to the standard's POSIX compliance level
   - Standards may extend but not modify

### ext4 (`standard=ext4`)

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
```
= /data/encrypted/ \
    ext4_flags=encrypt,casefold \
    ext4_project=42 \
    ext4_ea=security.selinux=system_u:object_r:data_t:s0,security.ima=0123456789abcdef \

= /data/encrypted/important.txt \
    ext4_flags=sync,immutable \
```

### UFS2/FFS (`standard=ufs2`)

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
```
= / standard=ufs2
= /var/db/secure/ \
    posix_mode=rwxr-x--- \
    posix_owner=root \
    posix_group=wheel \
    ufs_flags=sappnd,schg \
    ufs_ea=security.mac_biba=high \
    modified=2024-11-12T15:30:00Z
```

### CBM DOS (`standard=cbmdos`)

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
= / standard=cbmdos
= /games/GAME.PRG \
    cbm_type=PRG \
    cbm_name=GAME

= /data/SCORES.REL \
    cbm_type=REL \
    cbm_name=SCORES \
    cbm_record=30
```

#### Multi-Standard Example

```hra
= / standard=time,cbmdos,fat32
= /games/c64/GAME.PRG \
    cbm_type=PRG \
    cbm_name=GAME \
    fat_attrs=R \
    fat_name=GAME.PRG \
    modified=1984-12-24T12:00:00Z
```

#### Implementation Notes

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

### Atari DOS (`standard=ataridosii`)

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

#### Multi-Standard Example

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

#### Implementation Notes

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

### Implementation Considerations

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
```
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

