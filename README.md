# Arion Game: A JSON Schema for Documenting Sportsbook Market Rules
> A sports bet may have a short name on screen but a much longer set of rules. A **JSON Schema** helps teams check the shape of a file that stores those market rules. The aim is to **keep key terms clear and catch gaps in the data** before release.

<img width="1536" height="1024" alt="A JSON rulebook in orange braces" src="https://github.com/user-attachments/assets/7c9ad5d0-9508-4d91-9433-cab58a1d87b0" />
<br>
<br>

The site **[Arion Game](https://ariongame.net/)** publishes sports betting articles, but its own rule schema could not be found. This guide uses a **sample format**, not code taken from that site or any live betting system. For Filipino adults aged **21 and above**, this guide covers rule files, not placing sports bets.

## Give Each Market Rule Its Own Stable Record

A market name may change when a site alters the words shown on its main sports page. A **market ID** gives the rule a fixed name that does not depend on those screen labels. Teams can use that key to find the same rule in notes, tests, and past support cases.

A record should state **the sport and market name** so readers know which bet it covers. A rule about a match score **may not apply to a bet on just one half**. Those two rules should have **distinct keys**, even when the same teams take part in the match.

The **rule version** helps teams tell an old set of terms from a new set of terms. A date can show **when the new terms take effect, if that date has been confirmed**. **A new version should not erase the terms that were in force at an earlier date.**

## Set Clear Bounds for the Match Time Used

A rule about a football match should state **which part of the match counts toward the result**. In this sample, normal time **includes added time but leaves out both extra time and shootouts**. A **match period field** can make that scope clear without relying on the market name alone.

The schema below gives the field a **short list of allowed values** rather than free text. A value outside that list will **fail the check**, even if it looks like a valid word. The **scope note** adds detail for each choice, including cases that fall under the other label.

## Build a Small Schema That Checks Rule Files

The sample uses **Draft 2020-12**, a version of JSON Schema with its own set of keywords. The schema sets out **which fields must be present and which kinds of values they can hold**. It **does not fetch sports data, take funds, or work out the result of a live bet**.

**Example 1 — Market Rule Schema (`market-rule.schema.json`)**

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "additionalProperties": false,
  "required": [
    "marketId",
    "sport",
    "marketName",
    "ruleVersion",
    "effectiveDate",
    "matchPeriod",
    "scopeNote",
    "voidRule",
    "source"
  ],
  "properties": {
    "marketId": {
      "$ref": "#/$defs/nonblank"
    },
    "sport": {
      "$ref": "#/$defs/nonblank"
    },
    "marketName": {
      "$ref": "#/$defs/nonblank"
    },
    "ruleVersion": {
      "$ref": "#/$defs/nonblank"
    },
    "effectiveDate": {
      "type": "string",
      "format": "date"
    },
    "matchPeriod": {
      "enum": [
        "regulation",
        "first_half",
        "other"
      ]
    },
    "scopeNote": {
      "$ref": "#/$defs/nonblank"
    },
    "voidRule": {
      "$ref": "#/$defs/nonblank"
    },
    "source": {
      "$ref": "#/$defs/nonblank"
    }
  },
  "$defs": {
    "nonblank": {
      "type": "string",
      "pattern": "\\S"
    }
  }
}
```

The **required list** names fields that each rule file must contain before it can pass the check. A field with a string type must hold **text, rather than a number or a Boolean value**. The **pattern check requires at least one nonspace character**, so empty text and blank strings both fail.

The setting for **additionalProperties** rejects fields that the schema does not list as part of its design. The **`$defs` and `$ref` keywords** let fields share the same check for text that is not blank. It also means teams must **update the schema when they need to add a new rule field**.

## Keep Source Notes With the Terms They Support

A rule file should point to **the source text that backs up the terms it records**. A source could be **a rule book, a dated notice, or a saved copy of published terms**. The source field in this sample holds text, so **it does not check that the source exists**.

An **effective date** tells readers when terms start to apply, not when someone first wrote them down. Teams must **enable date format checks** if their test tool treats the format keyword as a note. **A valid date still does not prove the terms were in force on that particular day.**

## Test the File With Good and Bad Data

A test should start with a rule file that has a valid value for each required field. Removing the market ID from that file should then **cause the check to report a missing field**. The test should also **reject an impossible date when the tool has date format checks turned on**.

**Example 2 — Valid Sample Record (`market-rule.example.json`)**

```json
{
  "marketId": "demo-football-01",
  "sport": "football",
  "marketName": "Match result",
  "ruleVersion": "1.0",
  "effectiveDate": "2026-11-25",
  "matchPeriod": "regulation",
  "scopeNote": "Includes added time; excludes extra time and shootouts.",
  "voidRule": "Illustrative rule: abandoned matches are void.",
  "source": "Fictional rule sheet, revision 1.0"
}
```

*Illustrative data only. This record does not represent confirmed Arion Game market terms.*

A second test can put an unknown value in the field that states which match period counts. The check should **reject that value because it falls outside the set of permitted period labels**. A rule version filled with spaces **should also fail, since it has no actual version text**.

A third test can give the rule a new field that the schema does not define. The check should **reject that field, even if the rest of the file meets all the rules**. **Tests should cover bad data as well as data that is known to meet the rules.**

## Keep Rule Text Separate From Decisions About Bets

The sample stores a note about void bets as text, so it can hold rules with exceptions. It **does not decide when a match has been called off or when a bet is void**. **A schema check cannot establish that a rule is fair, lawful, or true to its source.**

A person should **compare the text with the cited source** before the file gets marked as reviewed. That review should also check **whether two parts of the same rule seem to contradict each other**. A clear record of changes helps the next reader see **what was checked and what remains unclear**.

## Publish Rule Files With a Clear Review Trail

A GitHub project can keep the **schema and rule files apart** so each has a clear role. A file change can then be reviewed with the **old and new lines side by side**. The project should note **which schema draft, test tool, and tool version** were used for those checks.

The files should contain rules and source notes, not the names or account details of real players. **Private bet slips and payment records do not belong in a public set of test files.** Sample records can show how the format works without sharing the private facts of a real case.

The **[Arion Game sportsbook market rules](https://ariongame.net/blog/pba-betting-odds-pinnacle-latest-lines-and-pba-games/)** reference covers basketball market types rather than a full rule set. For readers in the Philippines, these records can help **separate published terms from claims about those terms**. **A well-formed rule file is useful only when its claims can be traced and checked.**
