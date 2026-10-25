# Medicine sheet: ind-hi-indiccorp (rajiviyer)

151 documents reviewed: 137 clean, 12 fix, 2 drop.
Disease counts: F1: 8, C1: 2, C3: 2, C4: 1, G1: 1. These counts cover the
reviewed documents only. F1 is the only disease above the five-document threshold.
Update the counts after reviewing the rest of the pack.

## F1: Personal data

### Examples

Offsets below are zero-based Unicode character positions in the original text.
Only types and positions are recorded; personal values and their hex are omitted.

- `f9000a9730dae0f2`: phone number at offset 19.
- `954b27485b7c0a57`: private-person residential location and identity at offsets
  16 and 99.
- `2b7609cd44409223`: school/locality at offset 0; child and parent identity at
  71; further school/locality/class details at 281 and child identity at 330.

Other F1 documents: `9031920b80e46fd3`, `ebe1660c7122bfa0`, `abedb7f2c18bb7c7`,
`22f07929bcd3fe6b`, and `43809153c133f60b`.

### Rule and action

Replace confirmed personal details with `[REDACTED]`; retain the news narrative.
Do not drop the document.

A reviewer must identify each full start/end range in the original text,
including repeated identifying details. End positions are exclusive.
The offsets in labels.csv indicate evidence starts, not complete ranges.

Do not automatically redact every name, number or place. Consider the context,
including whether someone is mentioned in a public professional role.

The function applies reviewed ranges; it does not detect personal data.
It preserves surrounding text, including ZWJ/ZWNJ, and replaces ranges in
reverse order to keep offsets valid.

Run the gold-set harm test before using this rule at scale.

```python
def redact_confirmed_spans(text, spans):
    """Redact manually confirmed original-text spans; never log their values."""
    ordered = sorted(spans)
    previous_end = 0
    for start, end in ordered:
        if not (0 <= start < end <= len(text)) or start < previous_end:
            raise ValueError("Invalid or overlapping redaction spans")
        previous_end = end
    for start, end in reversed(ordered):
        text = text[:start] + "[REDACTED]" + text[end:]
    return text
```

### Harm test: pending

Not yet run; awaiting the Hindi gold set approved by two readers.
Will report documents tested, changed and dropped.
The medicine fails if more than 1% of gold documents are changed or dropped.

### Possible mistakes

- Numeric patterns may flag dates, quantities or other identifiers as phones.
- Removing names or places unnecessarily can lose useful reporting context.
- Redacting only one mention may leave a person identifiable elsewhere.
- Readers may choose different ranges; agree on the review criteria first.

## Other diseases

C1: 2 documents; C3: 2 documents; C4: 1 document; G1: 1 document.
These are below the threshold for a required medicine section.

C4 example: `972d8cf63e8d0ed9` joins unrelated tax-raid reporting, a bank-directory
passage starting at offset 503, and an impeachment passage starting at offset
1274. It is labelled fix. Verify the source and split the passages into separate
documents where reliable boundaries can be established.

No general boilerplate-removal, truncation-drop, document-splitting or Indic-repair
rule has been tested yet.
