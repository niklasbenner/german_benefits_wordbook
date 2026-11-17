# Job Amenity Wordbook

**A dictionary of positively advertised job attributes in German online job vacancies.**
- The wordbook contains **3,628 keyword entries** assigned to **50 active job attributes** across **nine amenity dimensions**: compensation, benefits, scheduling, employment security, career opportunities, job content, organizational culture, workplace resources, and mobility.
- Validation uses an **independent holdout of 14,481 job advertisements**, aggregate-dimension F1 scores range from **0.842 to 0.962** relative to the reference classification.


## Contents

1. [Validation summary by dimension](#validation-summary-by-dimension)
2. [Files and data structure](#files-and-data-structure)
3. [Taxonomy and attribute definitions](#taxonomy-and-attribute-definitions)
4. [How the dictionary was constructed](#how-the-dictionary-was-constructed)
5. [Validation design and key findings](#validation-design-and-key-findings)

### Validation summary by dimension

| Dimension | Attributes | Keyword entries | Dictionary-positive share | Precision | Recall | F1 |
|:--|--:|--:|--:|--:|--:|--:|
| Pay & Monetary Compensation | 6 | 459 | 57.1% | 0.971 | 0.902 | **0.935** |
| Fringe Benefits & Perks | 7 | 380 | 57.9% | 0.972 | 0.952 | **0.962** |
| Working Time, Flexibility & Work-Life | 10 | 706 | 59.0% | 0.967 | 0.933 | **0.950** |
| Employment Security | 2 | 103 | 32.9% | 0.934 | 0.914 | **0.924** |
| Career Development & Human Capital | 5 | 473 | 69.1% | 0.961 | 0.941 | **0.951** |
| Task Content, Autonomy & Meaning | 3 | 268 | 39.8% | 0.881 | 0.814 | **0.846** |
| Social Environment & Organizational Culture | 5 | 370 | 58.9% | 0.944 | 0.881 | **0.911** |
| Workplace Resources, Health & Accessibility | 4 | 253 | 25.6% | 0.922 | 0.775 | **0.842** |
| Location, Commuting & Mobility Benefits | 8 | 378 | 39.4% | 0.965 | 0.904 | **0.933** |
| **Active total** | **50** | **3,390** | — | — | — | — |
| Excluded concepts | 5 | 238 | — | — | — | — |
| **All documented concepts** | **55** | **3,628** | — | — | — | — |

**Keyword entries** count expressions assigned to each group or attribute in the supplied wordbook breakdown; they are not frequencies of occurrence in job postings. The total includes 3,390 entries assigned to active attributes and 238 entries documented under `excluded`. These are counts of category–expression entries and need not represent the number of distinct strings after deduplication across attributes.

The **dictionary-positive share** is the fraction of validation vacancies with at least one dictionary match in the group, rather than the reference-label prevalence or the share of vacancies offering the benefit in reality.

## Files and data structure

The same underlying dictionary is distributed in two representations (the filenames below can be adjusted to match the repository):

| File | Format | Purpose |
|:--|:--|:--|
| `wordbook_benefits_v1.json` | Nested JSON | Direct lookup of expressions by group and individual attribute. |
| `wordbook_benefits_v1.csv` | Flat CSV | Analysis, inspection, joins, and manual review; one row per keyword. |

## Taxonomy and attribute definitions

Expand a dimension to view its **substantive definition and complete attribute-level validation results**. Each table combines the `snake_case` attribute identifier, a short definition, the number of dictionary keywords, and all reported validation statistics. Each of the 50 active attributes belongs to exactly one of the nine dimensions, although an individual vacancy may match multiple attributes or dimensions.

The **group aggregate** row is positive if at least one constituent attribute is identified; its validation metrics are computed for this aggregate indicator rather than averaged across attributes. **Positive share** is the share of the 14,481 held-out advertisements classified as positive by the dictionary, `(TP + FP) / N`. Precision, recall, and F1 compare the dictionary with the reference classification. Keyword counts measure expressions in the wordbook, **not** their frequency in vacancy texts. See [Validation design and key findings](#validation-design-and-key-findings) for definitions and interpretation.

<details>
<summary><strong>1. Pay &amp; Monetary Compensation</strong> — 6 attributes; 459 keyword entries; group F1 = 0.935</summary>

Captures positive pay statements and additional monetary compensation. Ordinary or neutral references to salary are not counted as positive salary claims; collectively bargained pay and above-tariff pay are differentiated.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **459** | **57.1%** | **0.971** | **0.902** | **0.935** |
| `salary` | Positive statements about regular pay or remuneration, not neutral mentions of salary. | 82 | 33.8% | 0.921 | 0.771 | 0.839 |
| `additional_fixed_payments` | Christmas or holiday payments, a thirteenth salary, and similar fixed additional payments. | 61 | 20.2% | 0.972 | 0.876 | 0.922 |
| `collectively_bargained_pay` | Pay explicitly tied to a collective agreement or formal pay scale. | 100 | 18.3% | 0.958 | 0.864 | 0.908 |
| `bonuses` | Variable, performance-, sales-, or success-dependent compensation. | 106 | 12.0% | 0.829 | 0.734 | 0.778 |
| `allowances` | Additional pay for shifts, night work, weekends, locations, or functions. | 41 | 5.4% | 0.914 | 0.779 | 0.841 |
| `above_tariff_pay` | Compensation explicitly above collectively agreed or customary pay rates. | 69 | 3.6% | 0.819 | 0.830 | 0.824 |

</details>

<details>
<summary><strong>2. Fringe Benefits &amp; Perks</strong> — 7 attributes; 380 keyword entries; group F1 = 0.962</summary>

Captures employer-provided financial or in-kind benefits in addition to regular compensation, including occupational pensions, discounts, and food or leisure perks.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **380** | **57.9%** | **0.972** | **0.952** | **0.962** |
| `occupational_pension` | Employer-sponsored pension plans or retirement contributions. | 90 | 39.1% | 0.980 | 0.968 | 0.974 |
| `employee_discounts` | Discounts on the employer’s or partners’ goods and services. | 47 | 20.5% | 0.924 | 0.762 | 0.835 |
| `sports_and_leisure_benefits` | Fitness, sports, or leisure-related benefits offered by the employer. | 77 | 14.9% | 0.925 | 0.787 | 0.850 |
| `drinks` | Employer-provided drinks such as coffee, tea, or water. | 49 | 10.8% | 0.964 | 0.927 | 0.945 |
| `food_support_canteen` | Meal subsidies, food provision, or access to a subsidized canteen. | 77 | 10.7% | 0.918 | 0.844 | 0.879 |
| `capital_forming_benefits` | Employer contributions to employee savings plans (vermögenswirksame Leistungen). | 15 | 9.5% | 0.985 | 0.952 | 0.968 |
| `snacks_and_fruit` | Employer-provided snacks, fruit, or comparable small refreshments. | 25 | 6.2% | 0.979 | 0.878 | 0.926 |

</details>

<details>
<summary><strong>3. Working Time, Flexibility &amp; Work-Life</strong> — 10 attributes; 706 keyword entries; group F1 = 0.950</summary>

Covers spatial and temporal flexibility, leave, family support, and advertised work-life compatibility. Flexibility and schedule predictability are deliberately distinguished.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **706** | **59.0%** | **0.967** | **0.933** | **0.950** |
| `flexible_working_hours` | Employee discretion over working-time arrangements, such as flexitime. | 102 | 32.2% | 0.957 | 0.885 | 0.920 |
| `at_least_30_days_of_vacation` | An explicitly advertised vacation entitlement of 30 days or more. | 62 | 22.1% | 0.959 | 0.965 | 0.962 |
| `remote_work` | Home office, mobile work, or other remote-work arrangements. | 38 | 19.3% | 0.964 | 0.926 | 0.945 |
| `family_and_care_benefits` | Concrete assistance with childcare, elder care, or other family responsibilities. | 109 | 10.4% | 0.938 | 0.767 | 0.844 |
| `family_friendliness` | Explicit statements that the employer or workplace is family-friendly. | 45 | 10.2% | 0.752 | 0.878 | 0.810 |
| `work_life_balance` | An explicitly promoted balance between work and private life. | 78 | 9.2% | 0.835 | 0.679 | 0.749 |
| `additional_leave` | Additional paid or special leave beyond ordinary vacation entitlements. | 92 | 6.7% | 0.915 | 0.642 | 0.755 |
| `part_time_option` | An explicitly offered part-time work arrangement. | 31 | 5.4% | 0.918 | 0.779 | 0.843 |
| `predictable_and_regular_working_hours` | Stable, reliable, or predictable schedules, distinct from flexible scheduling. | 80 | 3.6% | 0.812 | 0.602 | 0.691 |
| `working_week_below_40_hours` | An explicitly offered standard working week shorter than 40 hours. | 69 | 3.5% | 0.913 | 0.694 | 0.788 |

</details>

<details>
<summary><strong>4. Employment Security</strong> — 2 attributes; 103 keyword entries; group F1 = 0.924</summary>

Captures the security of the employment relationship, distinct from opportunities for career progression.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **103** | **32.9%** | **0.934** | **0.914** | **0.924** |
| `permanent_employment_contract` | An explicitly offered open-ended employment contract. | 36 | 21.4% | 0.962 | 0.958 | 0.960 |
| `job_security` | Explicitly advertised stable, secure, or crisis-resistant employment. | 67 | 17.2% | 0.855 | 0.811 | 0.832 |

</details>

<details>
<summary><strong>5. Career Development &amp; Human Capital</strong> — 5 attributes; 473 keyword entries; group F1 = 0.951</summary>

Captures opportunities to acquire skills or advance professionally; training, general development, mentoring, and promotions are separate concepts.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **473** | **69.1%** | **0.961** | **0.941** | **0.951** |
| `training` | Courses, qualifications, and other structured learning opportunities. | 98 | 48.5% | 0.957 | 0.911 | 0.934 |
| `general_development_opportunities` | Opportunities for personal or professional growth without a specific promotion promise. | 88 | 28.8% | 0.817 | 0.795 | 0.805 |
| `onboarding` | Structured induction and support during entry into a new position. | 88 | 22.9% | 0.967 | 0.888 | 0.926 |
| `advancement_opportunities` | Explicit prospects for promotion or career progression. | 134 | 9.1% | 0.904 | 0.658 | 0.762 |
| `coaching_and_mentoring` | Coaching, mentorship, or comparable individualized support. | 65 | 8.0% | 0.905 | 0.742 | 0.815 |

</details>

<details>
<summary><strong>6. Task Content, Autonomy &amp; Meaning</strong> — 3 attributes; 268 keyword entries; group F1 = 0.846</summary>

Captures intrinsic attributes of the job itself, such as variety, decision-making discretion, and purpose.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **268** | **39.8%** | **0.881** | **0.814** | **0.846** |
| `interesting_and_varied_tasks` | Diverse, stimulating, or otherwise explicitly interesting job tasks. | 86 | 29.5% | 0.823 | 0.814 | 0.818 |
| `autonomy_and_discretion` | Independent decision-making, responsibility, or freedom to shape work. | 110 | 18.0% | 0.891 | 0.706 | 0.788 |
| `meaningful_work` | Explicitly meaningful work or social purpose associated with the role. | 72 | 1.1% | 0.930 | 0.371 | 0.531 |

</details>

<details>
<summary><strong>7. Social Environment &amp; Organizational Culture</strong> — 5 attributes; 370 keyword entries; group F1 = 0.911</summary>

Captures positively advertised social and organizational conditions. Claims about culture and inclusion are treated as employer statements, not verified employee experiences.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **370** | **58.9%** | **0.944** | **0.881** | **0.911** |
| `organizational_culture_and_atmosphere` | Positive descriptions of the workplace culture, climate, or atmosphere. | 148 | 32.4% | 0.875 | 0.788 | 0.829 |
| `team_quality` | Positive statements about colleagues, cooperation, or the team. | 77 | 22.5% | 0.892 | 0.669 | 0.765 |
| `flat_hierarchies` | An explicitly non-hierarchical or flat organizational structure. | 48 | 17.9% | 0.956 | 0.920 | 0.938 |
| `company_events` | Social events, outings, and employer-organized gatherings. | 52 | 16.5% | 0.947 | 0.876 | 0.910 |
| `diversity_and_inclusion` | An explicit positive commitment to an inclusive or diverse workplace, not merely a generic legal disclaimer. | 45 | 4.2% | 0.722 | 0.699 | 0.710 |

</details>

<details>
<summary><strong>8. Workplace Resources, Health &amp; Accessibility</strong> — 4 attributes; 253 keyword entries; group F1 = 0.842</summary>

Captures advertised workplace resources, health initiatives, and accessibility. Some items (e.g., equipment) may also be necessary production inputs.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **253** | **25.6%** | **0.922** | **0.775** | **0.842** |
| `health_management` | Health and well-being initiatives, including employer-provided health-management activities. | 56 | 17.3% | 0.910 | 0.836 | 0.871 |
| `workplace_equipment` | The provision or quality of workplace equipment and facilities. | 155 | 7.8% | 0.841 | 0.573 | 0.682 |
| `employer_provided_digital_devices` | Laptops, mobile phones, or comparable devices provided by the employer. | 29 | 4.0% | 0.938 | 0.761 | 0.840 |
| `workplace_accessibility` | Explicit accessibility of the workplace or facilities. | 13 | 0.4% | 0.952 | 0.952 | 0.952 |

</details>

<details>
<summary><strong>9. Location, Commuting &amp; Mobility Benefits</strong> — 8 attributes; 378 keyword entries; group F1 = 0.933</summary>

Captures commuting convenience and employer mobility support. Company cars are grouped here in the baseline; an alternative specification can assign them to fringe benefits.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| **Group aggregate** | At least one attribute in this dimension is detected. | **378** | **39.4%** | **0.965** | **0.904** | **0.933** |
| `bicycle_scheme` | Employer-supported bicycles or bike-leasing schemes. | 57 | 19.3% | 0.956 | 0.953 | 0.954 |
| `public_transport_ticket` | Employer-provided or subsidized public-transport tickets. | 80 | 11.3% | 0.960 | 0.841 | 0.897 |
| `transport_accessibility` | Favorable access to the workplace via public transport or road networks. | 40 | 7.3% | 0.858 | 0.751 | 0.801 |
| `parking` | Availability of parking facilities. | 17 | 7.3% | 0.975 | 0.865 | 0.917 |
| `company_car` | An explicitly offered company car, excluding unambiguously shared pool vehicles. | 25 | 5.5% | 0.951 | 0.870 | 0.909 |
| `mobility_subsidy` | Financial support for commuting or transport expenses. | 67 | 4.0% | 0.726 | 0.649 | 0.685 |
| `free_parking` | Parking explicitly offered without charge; may also count as parking. | 37 | 4.0% | 0.959 | 0.832 | 0.891 |
| `housing_and_relocation_support` | Employer assistance with accommodation or relocation costs. | 55 | 2.1% | 0.897 | 0.626 | 0.737 |

</details>

<details>
<summary><strong>Excluded concepts</strong> — 5 documented attributes; 238 keyword entries, not part of baseline aggregates</summary>

These categories remain visible in the exported wordbook for transparency and diagnostic use, but they are **not** included in the nine analytic dimensions or the 50 active attributes.

| Attribute | Description | Keywords | Positive share | Precision | Recall | F1 |
|:--|:--|--:|--:|--:|--:|--:|
| `generic_social_benefits` | Generic references to social benefits without a sufficiently specific offer. | 24 | 9.5% | 0.741 | 0.890 | 0.809 |
| `insurance_benefits` | Insurance-related claims that do not reliably distinguish voluntary benefits from ordinary entitlements. | 62 | 4.8% | 0.541 | 0.668 | 0.598 |
| `protective_equipment` | Protective clothing or safety equipment that may be operationally or legally required. | 59 | 4.2% | 0.959 | 0.921 | 0.939 |
| `international_work_environment` | International workplace descriptions rather than a clearly defined individual amenity. | 70 | 3.4% | 0.812 | 0.526 | 0.639 |
| `co_determination` | References to employee representation or codetermination, excluded from the baseline amenity measure. | 23 | 0.6% | 0.778 | 0.733 | 0.754 |

</details>

## How the dictionary was constructed

The wordbook was developed as a **researcher-controlled, deterministic measure of positively advertised job attributes** in German online job advertisements. Candidate expressions are identified in the benefit-related sections of vacancies, as extracted by the section-identification (zoning) procedure. The underlying *conceptual definitions* are kept distinct from the *keyword lists*: an attribute is defined by what a vacancy statement means, while the dictionary captures concrete expressions that convey that meaning. This distinction allows the lexical instrument to be systematically improved without changing the meaning of the measured outcomes.

### Iterative development and validation

The procedure consists of four stages:

1. **Define the initial taxonomy.** We first explore benefit sections to identify the range of attractive job characteristics and develop an initial set of benefit concepts. Frequently occurring unigrams and n-grams provide candidate expressions, which are grouped according to their meaning. This frequency-based exploration is a starting point, **not a binding minimum-frequency rule for the final dictionary**: later refinement can introduce less frequent expressions and linguistic variants. For each attribute, we define positive cases, exclusions, and boundaries relative to related concepts, and translate these definitions into detailed category-specific coding instructions for the language model. The resulting taxonomy distinguishes, for example, flexible from merely predictable working hours, training from general career development or advancement, and an explicitly positive pay claim from a neutral mention of salary. Only clearly advertised positive characteristics are within scope; applicant requirements, negations, task descriptions without an amenity claim, and references solely to statutory minimum standards are not treated as benefits.

2. **Classify the development sample twice.** We apply the initial dictionary and **Qwen 3.8 27B** independently to the same development sample of **more than 10,000 vacancies**. The language model receives the attribute-specific definitions and coding instructions and determines whether each benefit is present. For each positive classification, it is required to provide the supporting passage from the advertisement; it also flags potentially relevant benefits or formulations not yet captured by the dictionary. Comparing the dictionary's explicit keyword matches with these evidence-based semantic classifications reveals both **false negatives** (phrases consistent with the definition but absent from the dictionary) and **false positives** (matches whose meaning does not satisfy the definition). The LLM is a reference for *development and comparison*, not a component of the final dictionary-based classifier.

3. **Refine the dictionary and prompt iteratively.** We examine differences between the two classifications, particularly the textual evidence returned by the language model. Promising new expressions are evaluated for inclusion, while existing expressions that systematically match the wrong context are considered for revision or removal. We assess candidate changes using **precision, recall, and especially the F1 score** against the semantic reference classification, rather than maximizing the raw number of matches. Refinement also concerns the coding instructions: systematic disagreements can reveal ambiguous definitions, prompting sharper rules for distinctions such as family-friendliness versus concrete family and care benefits, team quality versus organizational culture, or accessibility versus generic diversity statements. ChatGPT is additionally used as a consistency aid when assessing whether candidate expressions fit the intended semantic category. The dictionary and prompt are thus improved together, while category assignments and inclusion decisions remain researcher-controlled.

4. **Freeze and validate on unseen data.** Once the dictionary sufficiently reproduces the LLM-based classification during development, we **freeze the taxonomy, the coding prompt, and the keyword lists**. We then independently apply the final dictionary and the frozen Qwen coding procedure to an **out-of-sample holdout of 14,481 advertisements**. None of these held-out observations is used for adding or removing keywords, choosing thresholds, or refining prompts or category definitions. We calculate precision, recall, and F1 for each attribute and for each of the nine aggregate dimensions, where a dimension is positive if any constituent attribute is identified. The detailed results are shown in the expandable taxonomy tables above and interpreted in the [validation section](#validation-design-and-key-findings) below.

The aim of this process is **not to label every conceivable mention** of a benefit: expressions are retained only where they are consistent with the substantive category definitions. The finished wordbook can therefore be applied transparently and deterministically to the full corpus of approximately **11.4 million vacancy advertisements**, **without running an LLM on individual vacancies**. As with any lexical approach, it may miss unusual paraphrases or context-specific statements; the reported validation measures describe agreement with the frozen semantic reference classification, not verified realized working conditions.

## Validation design and key findings

### Out-of-sample evaluation

After freezing the dictionary and category-specific instructions, both the dictionary and the final semantic reference procedure were applied independently to the **same 14,481 held-out advertisements**. The evaluation reports the correspondence between the deterministic dictionary and the reference classification, for each attribute and for group aggregates. The accompanying methods description specifies a frozen, model-based semantic reference procedure. The validation statistics should therefore be interpreted as agreement with that reference classification rather than as direct proof of real-world benefit provision.

For a given attribute, the reported metrics are:

- **Dictionary-positive share:** `(TP + FP) / N`, where `N = 14,481`.
- **Precision:** `TP / (TP + FP)`, i.e., the fraction of dictionary positives also positive in the reference classification.
- **Recall:** `TP / (TP + FN)`, i.e., the fraction of reference positives recovered by the dictionary.
- **F1:** `2 × precision × recall / (precision + recall)`.

Group-level metrics are calculated after aggregating constituent attributes with the logical OR rule; they are **not averages** of the individual attribute metrics. The positive share therefore need not equal the sum of individual shares.
**Table note.** Positive share is the share of the validation sample identified as positive by the **dictionary**, `(TP + FP) / N` with `N = 14,481`. Precision, recall, and F1 are evaluated relative to the reference classification. Group-level indicators equal one if any constituent attribute is present. Excluded concepts are reported for transparency only.

### Interpretation of the results

Across the **nine aggregate dimensions**, F1 scores range from **0.842** (*Workplace Resources, Health & Accessibility*) to **0.962** (*Fringe Benefits & Perks*), with seven of the nine dimensions above **0.91**. Aggregate precision ranges from **0.881** to **0.972**. This close correspondence supports the use of the frozen dictionary as a scalable approximation to the reference coding procedure.

Performance varies more strongly at the attribute level. Relatively concrete, standardized offers are identified particularly well, including `occupational_pension` (F1 = **0.974**), `at_least_30_days_of_vacation` (**0.962**), `remote_work` (**0.945**), `bicycle_scheme` (**0.954**), and `permanent_employment_contract` (**0.960**). More abstract or heterogeneous expressions tend to be harder to retrieve: for example, `meaningful_work` has recall **0.371** and F1 **0.531**, while `workplace_equipment` has recall **0.573** and F1 **0.682**. These differences underscore the intended precision-oriented, conservative character of the wordbook.

Importantly, the measures capture **attributes that employers choose to advertise**, not the full set of conditions actually provided to employees. A negative dictionary result can mean that an offer was not advertised, that no benefit section was available, or that a relevant formulation was not covered by the final expressions. The evaluation assesses the reproducibility of the semantic coding framework by the dictionary; it does not establish the underlying truth of employers' claims.
