## Patch meta.yml information
New patches should include a meta.yml for each patch with the following data:

```yaml
description*:
tags*:
status*:
issue_number*:
fr_doc:
reference:

patches*:
  '001':
    start_date:
    end_date:
```
*required

### Description
Overview of what the patch is. 
Ex: "Title 33 Volume 9 was assigned 2018 instead of 2019. This patch corrects the amendment date to be the correct year."

### Tags
One of these tags. If more than one apply, create a unique patch for each issue. This keeps patch responsibilities smaller and also makes setting start/end dates easier.
- structural
Generally GPO parser issues, DTD validation hacks, or print presentation hacks. These could be XML nodes that are being incorrectly inserted or nodes inserted only to change/aid print formatting.
- transcription
Editor didn't apply patch correctly, eg. left old tags behind. We don't update substantive content.
- amendment-date
Editor applied incorrect amendment date. Most commonly it's an incorrect year or month, but sometimes dates are inserted that are incorrect or unparsable.

### Status
`approved` or `needs-approval` by OFR

### Github Issue Number
The number of the issue in the ecfr-xml-corrections Github Repo that initially found and describes this problem.

### FR Doc
The FR Doc number of the document that is being patched. This is the FR Doc of the document that is being patched, not the FR Doc of the patch itself.

### Reference
When the patch is not approved, include items that help give context to the patch. These could be the FR Doc or print pdf. When the patch is approved link to the basecamp message that is relevant to the patch.

### Patches
This is the information required to apply the patch. Patches are numbered 001..999 and include either `start_date` and `end_date` or `first_issue_date` and `last_issue_date` attributes to determine if a title_version's `preprocessed_source_xml` file is eligible for this patch. 


--------------------------------------------------------------------------------

## How to Patch
https://github.com/criticaljuncture/criticaljuncture.org/wiki/ECFR.Versioner.HowToPatch

## AMDDATE Issues
### Amendment date ranges
Occasionally we'll see amendment dates that are ranges. These take place often when laws go into effect over a weekend, often for a temporary law (things like weekend firework displays or boating events that involve the Coast Guard).

Frequently these weekend laws will get bundled with other laws in a volume and then have an amendment date of the Monday after they would have been assigned to become effective. For example, an amendment date that reflects a Saturday of July 1, 2000 but has changes getting inserted with other laws that are effective Monday, July 3, 2000 will result in an inserted amendment date of July 3, 2000. 

When these range dates are inserted, CFR says to use the later date. If they seem unusual/incorrect, we should contact them.

Original basecamp explanation:
https://criticaljuncture.basecamphq.com/projects/4648449-federal-register-2-0/posts/108715908/comments#401012955

## TitleAmddateCsvGenerator
We've created a utility that makes it easier to diagnose and triage amendment date issues.

The TitleAmddateCsvGenerator creates a CSV with all the volume amendment dates for every title_version received for a Title in a given year.

CSV output example:
```
| 2017-01-03       |  2017-02-03         |  2017-03-15     |
|----------------------------------------------------------|
| January 1, 2017  |  *January 5, 2016*  |  January 5, 2017|
| January 1, 2017  |  January 5, 2016    |  January 5, 2017|
| January 1, 2017  |  ***Jan***          |  January 5, 2017|
```

The first row of dates are the received_on date for the source_xml for a title version. Column data is every volume amendment date for a single title_version.

For the source_xml for 2017-02-03 the amendment date is incremented from January 1 to January 5, but the year was accidentally decremented to 2016. Mostly commonly we see this error when incrementing from December of one year to January of the next - the month increment but the year doesn't resulting in a `reverse_amendment_date` issue.

The amendment date wraps dates that are unparsable or that are smaller than the previous date in *asterisks* so after csv generation it's easy to find amendment date problems while looking at the document in a spreadsheet.

Use from inside `ecfr-versioner` repo:

`$ TITLE=7 YEAR=2017 ruby lib/utils/title_ammdate_csv_generator.rb`

## Bulk patch unicode character replacement example
Example bulk replacement of em-dash with dash example:
`find . -type f -name '*.patch' -exec sed -i '' s%\xe2\x80\x93%-%g {} +`
Example of pulling the escape codes for an em-dash:
`echo "–" | hexdump -C`

## Ecfr Wiki
https://github.com/criticaljuncture/criticaljuncture.org/wiki/ECFR
