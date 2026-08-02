# EPC Data Quality Report: Camden, London

> **Correction, 2 August 2026.** This report originally led with a Critical finding
> stating that the register lost the ability to distinguish private from social rentals
> after 2012. That finding was wrong and has been withdrawn.
>
> The observation behind it was correct: the transaction_type column does drop the
> "rental (private)" and "rental (social)" values after 2012. What was not checked was
> whether the information survived elsewhere. It did. The tenure column carries the
> distinction across every year from 2012 to 2026.
>
> The finding has been rewritten around what is actually wrong, which is that tenure
> cannot be determined for 14.2% of certificates, and demoted from Critical to High.
> The remaining findings are unchanged and have been renumbered.

## About this data

An EPC, an Energy Performance Certificate, is a rating every home in England and Wales needs when it is sold, rented out, or newly built. It scores a home's energy efficiency from A (best) to G (worst), based on things like insulation and heating. This report looks at 102,185 domestic EPCs for Camden, London, covering January 2012 to June 2026, taken from the government's own EPC register. In practice, this data is used to check whether rented homes meet the legal energy standard, to track how efficient the housing stock is, and to plan for net zero.

This report takes the findings from my profiling notebook, ranks them by how serious they are, worst first, and updates two of them with evidence from the later analysis.

## Severity levels

- **Critical**: leads to wrong decisions or makes the data unusable for its main purpose.
- **High**: badly distorts counts or analysis; every user has to work around it.
- **Medium**: impossible or contradictory values that slip through; limited in number but wrong.
- **Low**: minor or expected; little or no action needed.

## What the register does well

Before the problems, it is worth saying what works, because it matters for the conclusion. Certificate numbers are unique, with no duplicates across all 102,185 records. No certificate is dated in the future. And the energy rating letter matches its numeric score in 100% of records, every single one. This last point is important: it proves the register can enforce a rule perfectly when it has one. So the problems in this report are not bad luck or unavoidable mess. They are the places where no rule was applied.

## Summary of findings

| # | Finding | Severity |
|---|---------|----------|
| 1 | No way to tell if a record is still current | Critical |
| 2 | Certificates are not the same as properties | High |
| 3 | Tenure cannot be determined for 14.2% of certificates | High |
| 4 | The same assessment gets lodged more than once on the same day | Medium |
| 5 | 1,100 certificates were lodged before the inspection happened | Medium |
| 6 | Impossible values pass straight through with no checks | Medium |
| 7 | Category labels drift over time | Low |

## Findings, worst first

### 1. There is no way to tell if a record is still current

**Severity: Critical**

The register has no status field: nothing marks a certificate as current, replaced, or the property demolished. This becomes a real problem when a building is rebuilt. At one Hampstead building, the same flat addresses appear twice with facts that contradict each other: in 2016 a flat is 23 m2, rated D, in a building described as "before 1900", then in 2018 the same address is 119 m2, rated C, built "2017". The building was knocked down and rebuilt, so the old records now point at flats that no longer exist, but nothing in the data says so.

The cost of this is measurable. Of the privately rented homes whose most recent certificate shows a rating of F or G, which is unlawful to let without a registered exemption, 249 in total, only 51 were inspected in 2023 or later. 135 were last inspected between 2012 and 2017. Nothing requires a certificate to be refreshed after improvement work, so the register cannot distinguish a home that is still F or G from one that was upgraded years ago and never recertificated.

This is rated Critical because it produces wrong decisions about specific properties. A regulator working from this data to enforce the minimum standard would be acting on records that may be a decade out of date, with no way to tell which ones.

**Where I found it:** Section 3, Uniqueness, and the analysis notebook.

### 2. Certificates are not the same as properties

**Severity: High**

There are 92,883 certificates with a UPRN, but they only cover 71,513 distinct UPRNs, because homes get re-certificated every time they are sold or re-let. So counting certificates overstates how many homes exist. But 71,513 is not the real number of homes either: some UPRNs are building-level, not home-level, and one covers 69 separate flats in a single block. This means the count is too high in one way (the same home repeated across certificates) and too low in another (many flats hidden under one UPRN), and nothing in the data tells you by how much. The honest conclusion is that you cannot get a true property count from this dataset alone.

**Where I found it:** Section 3, Uniqueness.

### 3. Tenure cannot be determined for 14.2% of certificates

**Severity: High**

Tenure records whether a home is owner-occupied, privately rented or socially rented. It matters because the minimum energy standard applies to private rentals specifically, so enforcement depends on knowing which homes those are.

The register does record this. The tenure column carries the private and social split across every year from 2012 to 2026. But on 14,509 certificates, 14.2% of the total, tenure is either recorded as "unknown" or left blank, so it cannot be determined whether the law applies to those properties at all. The share moves between 5.7% and 17.6% across the period without any clear trend, so this is inconsistent recording rather than a decline over time.

There is a second limit on how far the column can be trusted. Of homes with a usable tenure on more than one certificate, 36.5% show a different tenure between records. Some of that is genuine, since properties do move between being owner-occupied and rented, but it means tenure is a snapshot taken on inspection day rather than a durable fact about the property.

A separate issue, worth recording because it originally led to a wrong conclusion in this report: the transaction_type column did drop its "rental (private)" and "rental (social)" values after 2012. That is genuine vocabulary drift, but it carries no regulatory consequence, because the same information is held in tenure. It is covered under finding 7.

**Where I found it:** Section 2, Completeness, and the analysis notebook.

### 4. The same assessment gets lodged more than once on the same day

**Severity: Medium**

There are 475 extra certificates where the same home, at the same address, was certificated more than once on the same day. In the worst case, one flat in Kentish Town was certificated five times in a single day, and all five are identical: same rating, same score, same floor area, same emissions, lodged minutes apart. It is one assessment registered five times over. Certificate numbers are always unique, so nothing stops the same job being lodged again and again.

**Where I found it:** Section 3, Uniqueness.

### 5. 1,100 certificates were lodged before the inspection happened

**Severity: Medium**

There are 1,100 certificates where the lodgement date comes before the inspection date, which is impossible: the record was filed before the visit that produced it. What makes this more than a handful of typos is the pattern. Every one of the 1,100 falls in 2012, 2013 or 2014, and then it stops completely. They also cluster tightly at 88 to 90 days backwards, not scattered randomly. That is the fingerprint of a system or batch process from that era, not human error. The records are wrong in a way a person could not produce by hand, and they are still sitting in the published data years later, unflagged.

**Where I found it:** Section 4, Validity.

### 6. Impossible values pass straight through with no checks

**Severity: Medium**

Some records hold values that cannot physically be true, and nothing stopped them being lodged. Floor area runs from 3 m2, smaller than a prison cell, up to 3,337 m2 recorded as a single house. 57 records describe homes below the legal minimum room size for one adult in a shared house. Some certificates carry negative CO2 emissions, which would mean a home emitting less than nothing, and there is no solar or renewable record to explain it. Each of these is a field where no plausibility check exists, so a wrong number is accepted just as easily as a right one.

**Where I found it:** Section 4, Validity.

### 7. Category labels drift over time

**Severity: Low**

Some category fields are not kept to a fixed list, so the same thing is written several ways. The transaction_type field has 20 different values, but two of them are just capital-letter versions of others ("Marketed sale" and "marketed sale"), and "non marketed sale" and "non-marketed sale" are split only by a hyphen. The tenure field has the same problem, with "Owner-occupied" and "owner-occupied" recorded separately. A filter that looks for one spelling silently drops the records written the other way.

The same column also changed its vocabulary. transaction_type used "rental (private)" and "rental (social)" in 2012, then dropped both from 2013 onward and recorded every rental as plain "rental". This report originally treated that as a Critical loss of regulatory information. It is not, because the tenure column holds the same distinction throughout. It is drift, like the casing variants, and it belongs here.

The labels were not typed by hand: the pattern points to software dropdown lists that changed between versions. It is low harm on its own, but it is easy to trip over if you do not know it is there.

**Where I found it:** Section 5, Consistency.

## The pattern behind all of this

Across every finding, the same thing is true. Where the register enforces a rule, it holds without exception: certificate numbers are always unique, and the rating letter always matches its score. Where it does not enforce a rule, impossible and contradictory records get in and stay, unflagged, for years. A 3 m2 house, a certificate filed before its own inspection, a home whose records cannot say whether it still exists in the form described: none of these are unavoidable noise. Each one exists in a specific place where a check was missing.

The most serious version of this is not a wrong value but a missing one. The register records what a property was on the day it was inspected, and never records that the day has passed. Nothing marks a certificate as superseded, nothing links a replacement to what it replaced, and nothing requires a certificate to be refreshed when the property changes. So the data can tell you what a home scored in 2014 and cannot tell you whether that is still true.

The cost lands on whoever uses this data next, for enforcement, research, or net zero planning, and it is a cost that better validation at the source would remove.

## Recommendations

**For the register team, in priority order:**

1. Add a status field so a record can be marked current, replaced, or demolished, and link a replacement certificate to the one it supersedes. This is the highest-value fix, since without it the register cannot say whether any record still describes the property.
2. Require tenure to be recorded, or record why it could not be. 14.2% of certificates cannot currently be assessed against the minimum standard because the tenure is unknown or blank.
3. Make the property ID (UPRN) required, and split it down to individual flats, not whole buildings.
4. Add basic sanity checks at the point of entry: floor area within a believable range, emissions never negative without a renewable source recorded, and inspection date always before lodgement date.
5. Block or clean up same-day repeat lodgements of the same assessment.
6. Fix or flag the 1,100 legacy records with impossible dates.
7. Lock category fields like transaction type and tenure to a fixed list, so the same value cannot be recorded two different ways.

**For anyone using this data right now, before those fixes happen:**

- When removing duplicate certificates, keep the latest one per property, but never merge records using UPRN alone, since one UPRN can cover many flats.
- Treat a certificate count as a certificate count, not a home count.
- Filter out impossible floor areas and negative emissions before doing any analysis.
- Use the tenure column for private and social rental status, not transaction_type, and treat it as a snapshot taken on inspection day rather than a fixed attribute of the property.

---

**Note on minimum energy standards:** Minimum Energy Efficiency Standards (MEES) currently apply to private rentals only, under the Energy Efficiency (Private Rented Property) Regulations 2015. The minimum is EPC band E now, rising to band C by October 2030. Social rentals sit outside those regulations but are being brought under a separate, similar standard confirmed in early 2026. Because the two are governed by different rules, tenure has to be recorded accurately for either to be enforced, which is why the gap described in finding 3 matters.
