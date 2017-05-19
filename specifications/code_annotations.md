# ThreatSpec Code Annotations

This specification documents ThreatSpec code annotations. The format is inspired by JSdoc and uses @tags embedded inside code comments.

Examples

This is a JavaScript example

    /**
    * @mitigates @webapp:FileSystem against unauthorised access with strict file permissions (#122)
    * @exposes @webapp:App to XSS injection with insufficient input validation (#124,#133)
    * @transfers @cwe_319_cleartext_transmission to User:Browser with non-sensitive information (#125)
    * @accepts arbitrary file writes to @webapp:FileSystem with filename restrictions (#106)
    */

    Book(title, author) {
      ...
    }

For more information about ThreatSpec, see http://threatspec.org

# Version

The current version is 0.1.0

Versioning is based on SemVer (http://semver.org).

# Parsers

A ThreatSpec code annotation parser should document which code annotation version it implements and should support all features in the relevent version of this document.

# Scope

The term _scope_ is used to refer to where in code a ThreatSpec annotation can or should occurr. For example, @alias tags are most likely to be in a global scope because the IDs could be referred to from anywhere. It may even make sense to declare aliases in separate files that can be shared between projects.

Other tags, such as @mitigates only really apply to code that takes an action, so would associated with class or function declarations. 

When a parser has access to the AST and comments associated with declarations, it can dynamically build a data-flow diagram.

# Common fields

## ID

Reference to a name string, used for brevity and as hash keys.

Format

  * Begins with @
  * Snake case (lowercase words separated by _)

Examples

    @weak_prng
    @cwe_123_abc

## NAME

Name string of a boundary, component or threat.

## BOUNDARY

Name string of a boundary.

## COMPONENT

Name string of a component.

## THREAT

Name string of a threat.

# Tags

## @alias

Create an ID reference to a boundary, component or threat. IDs are created automatically when parsed, but if you would like to refer to existing threats etc for brevity. then an @alias should be used.

Format

    @alias boundary|component|threat ID to NAME

Scope

  * Global
  * Class
  * Function

Examples

    @alias boundary @web to Web
    @alias threat @cwe_123_abc to does something really bad

Example regular expression

    /^(?:[\s\*]*)@alias (boundary|component|threat) (\@[a-z0-9_]+?) to (.+?)(\\)?\s*$/i

## @describe

Adds a longer description text to a boundary, component or threat

Format

    @describe boundary|component|threat ID as DESCRIPTION

Scope

  * Global
  * Class
  * Function

Exmaples

    @describe component @crypto as Core cryptography kernel

Example regular expression

    /^(?:[\s\*]*)@describe (boundary|component|threat) (\@[a-z0-9_]+?) as (.+?)(\\)?\s*$/i

## @mitigates

A mitigation against a threat

Format

    @mitigates BOUNDARY:COMPONENT against THREAT with MITIGATION [(REFERENCES)]

Scope

  * Class
  * Function

Examples

    @mitigates Web:App against @cwe_123_abc with input validation
    @mitigates @app:Crypto against @weak_prng with strong PRNG from standard library [#123]

Example regular expression

    /^(?:[\s\*]*)@mitigates (.+?):(.+?) against (.+?) with (.+?)(?:\s*\((.*?)\)|(\\))?\s*$/i

## @exposes

An exposure to a threat

Format

    @exposes BOUNDARY:COMPONENT to THREAT with EXPOSURE [(REFERENCES)]

Scope

  * Class
  * Function

Examples

    @exposes @app:Crypto to @cwe_444_blah with meh

Example regular expression

    /^(?:[\s\*]*)@exposes (.+?):(.+?) to (.+?) with (.+?)(?:\s*\((.*?)\)|(\\))?\s*$/i

## @transfers

Transfers a threat to out of scope

Format

    @transfers THREAT to BOUNDARY:COMPONENT with TRANSFER [(REFERENCES)]

Scope

  * Class
  * Function

Examples

    @transfers @cwe_sucker to User:Browser with badgers

Example regular expression

    /^(?:[\s\*]*)@transfers (.+?) to (.+?):(.+?) with (.+?)(?:\s*\((.*?)\)|(\\))?\s*$/i

## @accepts

Accepts a threat

Format

    @accepts THREAT to BOUNDARY:COMPONENT with ACCEPTANCE [(REFERENCES)]

Scope

  * Class
  * Function

Examples

    @accepts @cwe_144_cats to Web:Cookies with lolmeh

Example regular expression

    /^(?:[\s\*]*)@accepts (.+?) to (.+?):(.+?) with (.+?)(?:\s*\((.*?)\)|(\\))?\s*$/i

# References

You can add references such as ticket numbers or URLs to the following tags: @mitigates, @exposes, @transfers and @accepts.

The references must be enclosed in \[\] square brackets and multiple references can be comma separated.

Examples

    @mitigates Web:Crypto against @cwe_123_abc with awesome sauce [#321]
    @exposes Web:Crypto to @cwe_531_cats with hardcoded password [#222,http://example.com/ignore_this]

# Multi-line

Some fields can be split into multiple lines for readability. Multi-line lines should end with a \\ backslash character to indicate a continuation on the next line.

References can be used along with multi-lines, but must be provided on the first line that begins with tag.

Examples

    @mitigates Web:Crypto against @cwe_123_abc with awesome sauce that \
    tastes really yummy.

    @mitigates Web:Crypto against @cwe_122_xyz with awesome sauce [#321] \
    tastes a bit too bitter.

# History
