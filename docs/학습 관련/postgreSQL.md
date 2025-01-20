# 로직컬
and, or, not

a > b
a < b
a <= b
a >= b
a = b 
a <> b
a != b

a BETWEEN b AND c
2 BETWEEN 1 AND 3 → t
2 BETWEEN 3 AND 1 → f

a NOT BETWEEN b AND c
2 NOT BETWEEN 1 AND 3 → f

2 BETWEEN SYMMETRIC 3 AND 1 → t
2 NOT BETWEEN SYMMETRIC 3 AND 1 → f

1 은 null과 다르다?
1 IS DISTINCT FROM NULL → t 
NULL IS DISTINCT FROM NULL → f

1 IS NOT DISTINCT FROM NULL → f 
NULL IS NOT DISTINCT FROM NULL → t

1.5 IS NULL → f
'null' IS NOT NULL → t

Square root
|/ 25.0 → 5

Cube root
||/ 64.0 → 4

@ -5.0 → 5.0

Bitwise AND
91 & 15 → 11

Bitwise OR
32 | 3 → 35

Bitwise exclusive OR
17 # 5 → 20

Bitwise NOT
~1 → -2

Bitwise shift left
1 << 4 → 16

Bitwise shift right
8 >> 2 → 2

'Post' || 'greSQL' → PostgreSQL
'Value: ' || 42 → Value: 42
btrim('xyxtrimyyx', 'xyz') → trim
text IS [NOT] [form] NORMALIZED → boolean
bit_length ( text ) → integer
char_length ( text ) → integer
character_length ( text ) → integer
lower ( text ) → text
lpad ( string text, length integer [, fill text ] ) → text

ltrim ( string text [, characters text ] ) → text
rtrim ( string text [, characters text ] ) → text
trim ( [ LEADING | TRAILING | BOTH ] [ characters text ] FROM string text ) → text
trim ( [ LEADING | TRAILING | BOTH ] [ FROM ] string text [, characters text ] ) →
text

normalize ( text [, form ] ) → text
octet_length ( text ) → integer
octet_length ( character ) → integer
overlay ( string text PLACING newsubstring text FROM start integer [ FOR
count integer ] ) → text
position ( substring text IN string text ) → integer
rpad ( string text, length integer [, fill text ] ) → text

substring ( string text [ FROM start integer ] [ FOR count integer ] )
    substring('Thomas' from 2 for 3) → hom
    substring('Thomas' from 3) → omas
    substring('Thomas' for 2) → Th

substring ( string text FROM pattern text ) → text
    substring('Thomas' from '...$') → mas

substring ( string text SIMILAR pattern text ESCAPE escape text ) → text
    substring('Thomas' similar '%#"o_a#"_' escape '#') → oma

substring ( string text FROM pattern text FOR escape text ) → text

unicode_assigned ( text ) → boolean
upper ( text ) → text

text ^@ text → boolean
    Returns true if the first string starts with the second string (equivalent to the starts_with() function).
    'alphabet' ^@ 'alph' → t

ascii ( text ) → integer
chr ( integer ) → text
concat ( val1 "any" [, val2 "any" [, ...] ] ) → text
concat_ws ( sep text, val1 "any" [, val2 "any" [, ...] ] ) → text
format ( formatstr text [, formatarg "any" [, ...] ] ) → text
initcap ( text ) → text
    initcap('hi THOMAS') → Hi Thomas

left ( string text, n integer ) → text
length ( text ) → integer
md5 ( text ) → text

parse_ident ( qualified_identifier text [, strict_mode boolean DEFAULT
true ] ) → text[]
    parse_ident('"SomeSchema".someTable') → {SomeSchema,sometable}

pg_client_encoding ( ) → name
    pg_client_encoding() → UTF8

quote_ident ( text ) → text
    quote_ident('Foo bar') → "Foo bar"

quote_literal ( text ) → text
    quote_literal(E'O\'Reilly') → 'O''Reilly'

quote_literal ( anyelement ) → text
    quote_literal(42.5) → '42.5'

quote_nullable ( text ) → text  
    quote_nullable(NULL) → NULL

quote_nullable ( anyelement ) → text
    quote_nullable(42.5) → '42.5'

regexp_count ( string text, pattern text [, start integer [, flags text ] ] ) →
    integer
    Returns the number of times the POSIX regular expression pattern matches in the
    string; see Section 9.7.3.
    regexp_count('123456789012', '\d\d\d', 2) → 3

regexp_instr ( string text, pattern text [, start integer [, N integer [, endoption integer [, flags text [, subexpr integer ] ] ] ] ] ) → integer
    Returns the position within string where the N'th match of the POSIX regular expression
    pattern occurs, or zero if there is no such match; see Section 9.7.3.
    regexp_instr('ABCDEF', 'c(.)(..)', 1, 1, 0, 'i') → 3
    regexp_instr('ABCDEF', 'c(.)(..)', 1, 1, 0, 'i', 2) → 5

regexp_like ( string text, pattern text [, flags text ] ) → boolean
    Checks whether a match of the POSIX regular expression pattern occurs within string;
    see Section 9.7.3.
    regexp_like('Hello World', 'world$', 'i') → t

regexp_match ( string text, pattern text [, flags text ] ) → text[]
    Returns substrings within the first match of the POSIX regular expression pattern to the
    string; see Section 9.7.3.
    regexp_match('foobarbequebaz', '(bar)(beque)') → {bar,beque}

regexp_matches ( string text, pattern text [, flags text ] ) → setof text[]
    Returns substrings within the first match of the POSIX regular expression pattern to the
    string, or substrings within all such matches if the g flag is used; see Section 9.7.3.
    regexp_matches('foobarbequebaz', 'ba.', 'g') →
    {bar}
    {baz}

regexp_replace ( string text, pattern text, replacement text [, start integer ] [, flags text ] ) → text
    Replaces the substring that is the first match to the POSIX regular expression pattern, or
    all such matches if the g flag is used; see Section 9.7.3.
    regexp_replace('Thomas', '.[mN]a.', 'M') → ThM

regexp_replace ( string text, pattern text, replacement text, start integer,
N integer [, flags text ] ) → text
    Replaces the substring that is the N'th match to the POSIX regular expression pattern, or
    all such matches if N is zero; see Section 9.7.3.
    regexp_replace('Thomas', '.', 'X', 3, 2) → ThoXa

regexp_split_to_array ( string text, pattern text [, flags text ] ) → text[]
    Splits string using a POSIX regular expression as the delimiter, producing an array of results; see Section 9.7.3.
    regexp_split_to_array('hello world', '\s+') → {hello,world}

regexp_split_to_table ( string text, pattern text [, flags text ] ) → setof
text
    Splits string using a POSIX regular expression as the delimiter, producing a set of results;
    see Section 9.7.3.
    regexp_split_to_table('hello world', '\s+') →
    hello
    world

regexp_substr ( string text, pattern text [, start integer [, N integer [,
flags text [, subexpr integer ] ] ] ] ) → text
    Returns the substring within string that matches the N'th occurrence of the POSIX regular
    expression pattern, or NULL if there is no such match; see Section 9.7.3.
    regexp_substr('ABCDEF', 'c(.)(..)', 1, 1, 'i') → CDEF
    regexp_substr('ABCDEF', 'c(.)(..)', 1, 1, 'i', 2) → EF

repeat ( string text, number integer ) → text
    Repeats string the specified number of times.
    repeat('Pg', 4) → PgPgPgPg

replace ( string text, from text, to text ) → text
    Replaces all occurrences in string of substring from with substring to.
    replace('abcdefabcdef', 'cd', 'XX') → abXXefabXXef

reverse ( text ) → text
    Reverses the order of the characters in the string.
    reverse('abcde') → edcba

right ( string text, n integer ) → text
    Returns last n characters in the string, or when n is negative, returns all but first |n| characters.
    right('abcde', 2) → de

split_part ( string text, delimiter text, n integer ) → text
    Splits string at occurrences of delimiter and returns the n'th field (counting from one),
    or when n is negative, returns the |n|'th-from-last field.
    split_part('abc~@~def~@~ghi', '~@~', 2) → def
    split_part('abc,def,ghi,jkl', ',', -2) → ghi

starts_with ( string text, prefix text ) → boolean
    Returns true if string starts with prefix.
    starts_with('alphabet', 'alph') → t

string_to_array ( string text, delimiter text [, null_string text ] ) →
text[]
    Splits the string at occurrences of delimiter and forms the resulting fields into a text
    array. If delimiter is NULL, each character in the string will become a separate element in the array. If delimiter is an empty string, then the string is treated as a single
    field. If null_string is supplied and is not NULL, fields matching that string are replaced
    by NULL. See also array_to_string.
    string_to_array('xx~~yy~~zz', '~~', 'yy') → {xx,NULL,zz}

string_to_table ( string text, delimiter text [, null_string text ] ) → setof
text
    Splits the string at occurrences of delimiter and returns the resulting fields as a set of
    text rows. If delimiter is NULL, each character in the string will become a separate
    row of the result. If delimiter is an empty string, then the string is treated as a single
    field. If null_string is supplied and is not NULL, fields matching that string are replaced
    by NULL.
    string_to_table('xx~^~yy~^~zz', '~^~', 'yy') →
    xx
    NULL
    zz

strpos ( string text, substring text ) → integer
    Returns first starting index of the specified substring within string, or zero if it's not
    present. (Same as position(substring in string), but note the reversed argument order.)
    strpos('high', 'ig') → 2

substr ( string text, start integer [, count integer ] ) → text
    Extracts the substring of string starting at the start'th character, and extending for
    count characters if that is specified. (Same as substring(string from start
    for count).)
    substr('alphabet', 3) → phabet
    substr('alphabet', 3, 2) → ph