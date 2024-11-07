# Human Readable Archive (hra)

A file that contains directory information and file contents and meta-data.

Example:

```hra
Human Readable
Archive
0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
# The first two lines are more expressive than they seem. They define the type of whitspace character and newline string used in the rest of the file.
# The second line holds the file format version.
# - Newline string used the rest of the file. The character or string after the version digits and before the prefix definitions.
# The second line defines the:
# - required meta string prefix is assigned to `=`
# - optional comment string prefix is assigned to `#`
# - optional redefine string prefix is assigned to `~`
# - optional escape string prefix is assigned to `/`
# - optional attribute assignment string is assigned to `=`
# - optional line continuation string is assigned to `\`
# - optional opener for contiguous strings is assigned to `"`
# - doubly optional closer of contiguous strings is assignd to '"'

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
```

## Format

### Header

The header is two lines and defines how to how to interpret the rest of the file.

#### First Line

The first line must start with `hra`. Then:
1. one character to be used as the space (and what this documentation refers to as *space*) character for the rest of the file
1. the format version (e.g. `0.1`) which contains
    1. the major version digits (version numbers us base 10 digits)
    1. a `.` separator
    1. the minor version digits
1. the newline character or string for the rest of the file

#### Second Line

The second line must contain the prefix string assignments.
- `meta`: Required. The string that indicates the line specifies a new file or directory is being defined.
- `comment`: Optional. The string that indicates the line is a comment.
- `redefine`: Optional. The string that indicates the line specifies a prefix redefinition.
- `escape`: Optional. The string that allows the prefixes to begin the line of a file data.
- `assignment`: Optional. The string that separates the attribute's name and value.
- `continuation`: Optional. The string that appears at the end of a line which indicates a line continuation.
- `opener`: Optional. The string that is prefixed to a contiguous string that only ends with the closer string.
- `closer`: Doubly Optional. If undefined, its value is the same as `opener`. It is also the only thing the escape prefix can escape in the contiguous string.

To define the prefixes:
1. The prefix name
1. No separating spaces
1. The prefix string. It may not contain spaces.
1. Optionally, to have more prefix assignments the following can be repeated:
    1. A single space
    2. More prefix assignments as described in the steps above.

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
1. Optionally, the opener string
1. A POSIX path. If referencing an already defined directory context, the path must not begin with a forward slash
1. If opened, the closer string

If the POSIX path ends with trailing single forward slash, a directory is specified and not a file.

Optionally (there are no required attributes), attributes can be associated with the definition with:
1. At least one leading spaces
1. Optionally, to allow multi-line attribute assignments the following the following can be included:
    1. The line continuation string
    1. Zero or more spaces
1. The attribute name string
1. The attribute assignment string
1. The attribute value string
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
There can be multiple

The redefinition prefix is 

## Directory Context

## Directory Inheritance

How a relative path inherits a directory path.

## Attribute Inheritance Chain

The HRA format uses a sequential inheritance chain to determine file and directory attributes when not explicitly specified.
First, any explicitly stated attributes take precedence.
If attributes are missing, the system traverses up the directory tree from the immediate parent to the root directory, using the first encountered set of attributes.
If no ancestor directories specify attributes, it examines child directories and uses the most commonly specified attributes among them (using the first child's attributes in case of ties).
Finally, if no attributes can be inherited through these means, the system falls back to default values: `rw-r--r--` and `root:root` for files, and `rwxr-xr-x` and `root:root` for directories.
Each attribute (permissions, user, group) is inherited independently, though certain attributes like symlink targets must always be explicitly specified.

## Preprocessors

The HRA format uses preprocessor verification through removal markers.
Each preprocessor listed in the file must remove its specific marker for the file to be valid.
If any markers remain, the HRA parser rejects the file as incompletely processed.

This provides two key benefits:
1. Guaranteed preprocessing completion - unprocessed files are invalid
1. Enforced preprocessing order - markers must be removed in sequence

The verification is achieved by embedding markers after the header that preprocessors must remove. Each marker:
- Has a unique random suffix to prevent collisions
- Names its required preprocessor
- Must be removed by its preprocessor for the file to be valid

This approach works with any preprocessor that can:
- Preserve non-processed lines unchanged
- Remove specific lines
- Process the file as text

Each preprocessor needs to:
1. Find and remove its specific marker
1. Leave all other marker lines unchanged

### Examples

1. **sed**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_SED_PREPROCESSING_x394nv84
```
```bash
sed '/REQUIRES_SED_PREPROCESSING_x394nv84/d' input.hra > output.hra
```

2. **awk**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_AWK_PREPROCESSING_k85m2p91
```
```bash
awk '!/REQUIRES_AWK_PREPROCESSING_k85m2p91/' input.hra > output.hra
```

3. **perl**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_PERL_PREPROCESSING_j73h1m52
```
```perl
perl -ne 'print unless /REQUIRES_PERL_PREPROCESSING_j73h1m52/' input.hra > output.hra
```

4. **python**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_PYTHON_PREPROCESSING_q62k9v37
```
```python
with open('input.hra') as infile, open('output.hra', 'w') as outfile:
    for line in infile:
        if 'REQUIRES_PYTHON_PREPROCESSING_q62k9v37' not in line:
            outfile.write(line)
```

5. **cpp** (C Preprocessor)
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_CPP_PREPROCESSING_w51t8x26
```
```bash
# Using cpp with a header that undefines the marker
echo '#define REQUIRES_CPP_PREPROCESSING_w51t8x26' > remove_marker.h
cpp -include remove_marker.h input.hra output.hra
```

6. **m4**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_M4_PREPROCESSING_n40r7u15
```
```bash
# Using m4 with a definition that removes the marker
echo 'define(`REQUIRES_M4_PREPROCESSING_n40r7u15',`')' > remove_marker.m4
m4 remove_marker.m4 input.hra > output.hra
```

7. **envsubst**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_ENVSUBST_PREPROCESSING_h29q6f84${REMOVE_MARKER}
```
```bash
REMOVE_MARKER= envsubst < input.hra > output.hra
```

8. **jq** (for JSON processing sections)
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_JQ_PREPROCESSING_b18p5d73
```
```bash
# Using jq with a wrapper script
jq_wrapper() {
    local tmp=$(mktemp)
    grep -v 'REQUIRES_JQ_PREPROCESSING_b18p5d73' "$1" > "$tmp"
    mv "$tmp" "$2"
}
jq_wrapper input.hra output.hra
```

9. **template (custom)**
```hra
hra 0.1
meta= comment# redefine~ escape/ assignment= continuation\ opener" closer"
REQUIRES_TEMPLATE_PREPROCESSING_v84m3c62
```
```python
# Custom template processor
def process_template(infile, outfile):
    with open(infile) as f, open(outfile, 'w') as out:
        for line in f:
            if 'REQUIRES_TEMPLATE_PREPROCESSING_v84m3c62' not in line:
                # Process template directives
                out.write(process_line(line))
```

### Key Implementation Notes

1. **Error Handling**
   - Preprocessors should verify their marker exists before removing it
   - Should fail if marker not found
   ```bash
   # Example error check in bash
   if ! grep -q "REQUIRES_XXX_PREPROCESSING" input.hra; then
       echo "Error: Marker not found" >&2
       exit 1
   fi
   ```

2. **Marker Uniqueness**
   - Use sufficiently random strings (e.g. 8+ hex chars)
   - Include preprocessor name to avoid confusion
   - Consider including version info if relevant

3. **Line Preservation**
   - Most preprocessors should preserve line numbers
   - Important for error reporting
   - Some may need wrapper scripts to ensure this

