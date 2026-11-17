# Job Amenity Wordbook

**A transparent dictionary of positively advertised job attributes in German online job vacancies.** The wordbook contains **3,628 keyword entries** assigned to **50 active job attributes** across **nine amenity dimensions**, plus **five excluded concepts**. It measures what employers **advertise** as attractive job conditions, not necessarily what employees ultimately experience.

The final measurement instrument is **deterministic**: once the dictionary is frozen, applying the same expressions and matching rules to the same text produces the same indicators. Language models assisted in refining category definitions and keyword coverage during development, but are **not required for classification of the full vacancy corpus**.

## Contents

1. [At a glance](#at-a-glance)
2. [Files and data structure](#files-and-data-structure)
3. [Taxonomy and attribute definitions](#taxonomy-and-attribute-definitions)
4. [How the dictionary was constructed](#how-the-dictionary-was-constructed)
5. [Classification and aggregation rules](#classification-and-aggregation-rules)
6. [Validation design and key findings](#validation-design-and-key-findings)
7. [Complete validation results](#complete-validation-results)
8. [Scope, limitations, and reproducibility](#scope-limitations-and-reproducibility)

## At a glance

- **3,628 keyword entries** cover **nine** broad dimensions of advertised job amenities: compensation, benefits, scheduling, employment security, career opportunities, job content, organizational culture, workplace resources, and mobility.
- **50** active attributes contain **3,390 keyword entries**; **five** additional concepts contain **238 entries** under `excluded` and do not enter baseline aggregates.
- The dictionary is applied to the **benefit-related sections** of German vacancy texts, identified by a separate section-classification (zoning) procedure.
- Validation uses an **independent holdout of 14,481 job advertisements**, not used during dictionary development.
- Aggregate-dimension F1 scores range from **0.842 to 0.962** relative to the reference classification.
- The finalized wordbook was used for deterministic measurement of approximately **11.4 million** vacancies in the associated research project.

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

The largest dimension by dictionary size is **Working Time, Flexibility & Work-Life** (706 entries), followed by **Career Development & Human Capital** (473) and **Pay & Monetary Compensation** (459). At the individual-attribute level, **Workplace equipment** (155), **Organizational culture and atmosphere** (148), and **Advancement opportunities** (134) have the most entries. Dictionary size reflects the number of lexical formulations, **not** how often the benefit is advertised or how accurately it is detected.

## Files and data structure

The same underlying dictionary is distributed in two representations (the filenames below can be adjusted to match the repository):

| File | Format | Purpose |
|:--|:--|:--|
| `wordbook_english.json` | Nested JSON | Direct lookup of expressions by group and individual attribute. |
| `wordbook_english.csv` | Flat CSV | Analysis, inspection, joins, and manual review; one row per keyword. |

**CSV schema:** `group,workbook,keyword`. Both `group` and `workbook` are lower-case `snake_case` identifiers; `keyword` preserves the original German expression. Here, `workbook` denotes the **individual attribute/subcategory identifier** (not an Excel workbook).

```csv
group,workbook,keyword
working_time_flexibility_work_life,remote_work,homeoffice
pay_monetary_compensation,additional_fixed_payments,weihnachtsgeld
excluded,generic_social_benefits,sozialleistungen
```

**JSON schema:** `{group: {workbook: [keyword, ...]}}`, with exactly the same identifiers as the CSV.

```json
{
  "working_time_flexibility_work_life": {
    "remote_work": ["homeoffice", "mobiles arbeiten"]
  },
  "excluded": {
    "generic_social_benefits": ["sozialleistungen"]
  }
}
```

*The excerpts above illustrate the schema; they are not a complete or verified extract of the published wordbook.*

For example, all expressions assigned to remote work can be retrieved with:

```python
import json

with open("wordbook_english.json", encoding="utf-8") as f:
    wordbook = json.load(f)

remote_terms = wordbook["working_time_flexibility_work_life"]["remote_work"]
```

The mapping to English identifiers changes the **labels**, not the German keyword expressions or their substantive coding definitions. To reproduce classification results, the text-preprocessing and matching procedure must also be held fixed.

## Taxonomy and attribute definitions

Each dimension below can be expanded to see its definition and the attributes it contains. The 50 active attributes are mutually assigned to one of the nine dimensions; a vacancy can still match **multiple** attributes or dimensions.

<details>
<summary><strong>1. Pay &amp; Monetary Compensation</strong> — 6 attributes; 459 keyword entries; group F1 = 0.935</summary>

Captures positive pay statements and additional monetary compensation. Ordinary or neutral references to salary are not counted as positive salary claims; collectively bargained pay and above-tariff pay are differentiated.

- **Salary** (`salary`; **82 keywords**): Positive statements about regular pay or remuneration, not neutral mentions of salary.
- **Additional fixed payments** (`additional_fixed_payments`; **61 keywords**): Christmas or holiday payments, a thirteenth salary, and similar fixed additional payments.
- **Collectively bargained pay** (`collectively_bargained_pay`; **100 keywords**): Pay explicitly tied to a collective agreement or formal pay scale.
- **Bonuses** (`bonuses`; **106 keywords**): Variable, performance-, sales-, or success-dependent compensation.
- **Allowances** (`allowances`; **41 keywords**): Additional pay for shifts, night work, weekends, locations, or functions.
- **Above-tariff pay** (`above_tariff_pay`; **69 keywords**): Compensation explicitly above collectively agreed or customary pay rates.

</details>

<details>
<summary><strong>2. Fringe Benefits &amp; Perks</strong> — 7 attributes; 380 keyword entries; group F1 = 0.962</summary>

Captures employer-provided financial or in-kind benefits in addition to regular compensation, including occupational pensions, discounts, and food or leisure perks.

- **Occupational pension** (`occupational_pension`; **90 keywords**): Employer-sponsored pension plans or retirement contributions.
- **Employee discounts** (`employee_discounts`; **47 keywords**): Discounts on the employer’s or partners’ goods and services.
- **Sports and leisure benefits** (`sports_and_leisure_benefits`; **77 keywords**): Fitness, sports, or leisure-related benefits offered by the employer.
- **Drinks** (`drinks`; **49 keywords**): Employer-provided drinks such as coffee, tea, or water.
- **Food support / canteen** (`food_support_canteen`; **77 keywords**): Meal subsidies, food provision, or access to a subsidized canteen.
- **Capital-forming benefits** (`capital_forming_benefits`; **15 keywords**): Employer contributions to employee savings plans (vermögenswirksame Leistungen).
- **Snacks and fruit** (`snacks_and_fruit`; **25 keywords**): Employer-provided snacks, fruit, or comparable small refreshments.

</details>

<details>
<summary><strong>3. Working Time, Flexibility &amp; Work-Life</strong> — 10 attributes; 706 keyword entries; group F1 = 0.950</summary>

Covers spatial and temporal flexibility, leave, family support, and advertised work-life compatibility. Flexibility and schedule predictability are deliberately distinguished.

- **Flexible working hours** (`flexible_working_hours`; **102 keywords**): Employee discretion over working-time arrangements, such as flexitime.
- **At least 30 days of vacation** (`at_least_30_days_of_vacation`; **62 keywords**): An explicitly advertised vacation entitlement of 30 days or more.
- **Remote work** (`remote_work`; **38 keywords**): Home office, mobile work, or other remote-work arrangements.
- **Family and care benefits** (`family_and_care_benefits`; **109 keywords**): Concrete assistance with childcare, elder care, or other family responsibilities.
- **Family-friendliness** (`family_friendliness`; **45 keywords**): Explicit statements that the employer or workplace is family-friendly.
- **Work-life balance** (`work_life_balance`; **78 keywords**): An explicitly promoted balance between work and private life.
- **Additional leave** (`additional_leave`; **92 keywords**): Additional paid or special leave beyond ordinary vacation entitlements.
- **Part-time option** (`part_time_option`; **31 keywords**): An explicitly offered part-time work arrangement.
- **Predictable and regular working hours** (`predictable_and_regular_working_hours`; **80 keywords**): Stable, reliable, or predictable schedules, distinct from flexible scheduling.
- **Working week below 40 hours** (`working_week_below_40_hours`; **69 keywords**): An explicitly offered standard working week shorter than 40 hours.

</details>

<details>
<summary><strong>4. Employment Security</strong> — 2 attributes; 103 keyword entries; group F1 = 0.924</summary>

Captures the security of the employment relationship, distinct from opportunities for career progression.

- **Permanent employment contract** (`permanent_employment_contract`; **36 keywords**): An explicitly offered open-ended employment contract.
- **Job security** (`job_security`; **67 keywords**): Explicitly advertised stable, secure, or crisis-resistant employment.

</details>

<details>
<summary><strong>5. Career Development &amp; Human Capital</strong> — 5 attributes; 473 keyword entries; group F1 = 0.951</summary>

Captures opportunities to acquire skills or advance professionally; training, general development, mentoring, and promotions are separate concepts.

- **Training** (`training`; **98 keywords**): Courses, qualifications, and other structured learning opportunities.
- **General development opportunities** (`general_development_opportunities`; **88 keywords**): Opportunities for personal or professional growth without a specific promotion promise.
- **Onboarding** (`onboarding`; **88 keywords**): Structured induction and support during entry into a new position.
- **Advancement opportunities** (`advancement_opportunities`; **134 keywords**): Explicit prospects for promotion or career progression.
- **Coaching and mentoring** (`coaching_and_mentoring`; **65 keywords**): Coaching, mentorship, or comparable individualized support.

</details>

<details>
<summary><strong>6. Task Content, Autonomy &amp; Meaning</strong> — 3 attributes; 268 keyword entries; group F1 = 0.846</summary>

Captures intrinsic attributes of the job itself, such as variety, decision-making discretion, and purpose.

- **Interesting and varied tasks** (`interesting_and_varied_tasks`; **86 keywords**): Diverse, stimulating, or otherwise explicitly interesting job tasks.
- **Autonomy and discretion** (`autonomy_and_discretion`; **110 keywords**): Independent decision-making, responsibility, or freedom to shape work.
- **Meaningful work** (`meaningful_work`; **72 keywords**): Explicitly meaningful work or social purpose associated with the role.

</details>

<details>
<summary><strong>7. Social Environment &amp; Organizational Culture</strong> — 5 attributes; 370 keyword entries; group F1 = 0.911</summary>

Captures positively advertised social and organizational conditions. Claims about culture and inclusion are treated as employer statements, not verified employee experiences.

- **Organizational culture and atmosphere** (`organizational_culture_and_atmosphere`; **148 keywords**): Positive descriptions of the workplace culture, climate, or atmosphere.
- **Team quality** (`team_quality`; **77 keywords**): Positive statements about colleagues, cooperation, or the team.
- **Flat hierarchies** (`flat_hierarchies`; **48 keywords**): An explicitly non-hierarchical or flat organizational structure.
- **Company events** (`company_events`; **52 keywords**): Social events, outings, and employer-organized gatherings.
- **Diversity and inclusion** (`diversity_and_inclusion`; **45 keywords**): An explicit positive commitment to an inclusive or diverse workplace, not merely a generic legal disclaimer.

</details>

<details>
<summary><strong>8. Workplace Resources, Health &amp; Accessibility</strong> — 4 attributes; 253 keyword entries; group F1 = 0.842</summary>

Captures advertised workplace resources, health initiatives, and accessibility. Some items (e.g., equipment) may also be necessary production inputs.

- **Health management** (`health_management`; **56 keywords**): Health and well-being initiatives, including employer-provided health-management activities.
- **Workplace equipment** (`workplace_equipment`; **155 keywords**): The provision or quality of workplace equipment and facilities.
- **Employer-provided digital devices** (`employer_provided_digital_devices`; **29 keywords**): Laptops, mobile phones, or comparable devices provided by the employer.
- **Workplace accessibility** (`workplace_accessibility`; **13 keywords**): Explicit accessibility of the workplace or facilities.

</details>

<details>
<summary><strong>9. Location, Commuting &amp; Mobility Benefits</strong> — 8 attributes; 378 keyword entries; group F1 = 0.933</summary>

Captures commuting convenience and employer mobility support. Company cars are grouped here in the baseline; an alternative specification can assign them to fringe benefits.

- **Bicycle scheme** (`bicycle_scheme`; **57 keywords**): Employer-supported bicycles or bike-leasing schemes.
- **Public-transport ticket** (`public_transport_ticket`; **80 keywords**): Employer-provided or subsidized public-transport tickets.
- **Transport accessibility** (`transport_accessibility`; **40 keywords**): Favorable access to the workplace via public transport or road networks.
- **Parking** (`parking`; **17 keywords**): Availability of parking facilities.
- **Company car** (`company_car`; **25 keywords**): An explicitly offered company car, excluding unambiguously shared pool vehicles.
- **Mobility subsidy** (`mobility_subsidy`; **67 keywords**): Financial support for commuting or transport expenses.
- **Free parking** (`free_parking`; **37 keywords**): Parking explicitly offered without charge; may also count as parking.
- **Housing and relocation support** (`housing_and_relocation_support`; **55 keywords**): Employer assistance with accommodation or relocation costs.

</details>

<details>
<summary><strong>Excluded concepts</strong> — 5 documented attributes; 238 keyword entries, not part of baseline aggregates</summary>

These categories remain visible in the exported wordbook for transparency and diagnostic use, but they are **not** included in the nine analytic dimensions or the 50 active attributes.

- **Generic social benefits** (`generic_social_benefits`; **24 keywords**): Generic references to social benefits without a sufficiently specific offer.
- **Insurance benefits** (`insurance_benefits`; **62 keywords**): Insurance-related claims that do not reliably distinguish voluntary benefits from ordinary entitlements.
- **Protective equipment** (`protective_equipment`; **59 keywords**): Protective clothing or safety equipment that may be operationally or legally required.
- **International work environment** (`international_work_environment`; **70 keywords**): International workplace descriptions rather than a clearly defined individual amenity.
- **Co-determination** (`co_determination`; **23 keywords**): References to employee representation or codetermination, excluded from the baseline amenity measure.

</details>

## How the dictionary was constructed

The dictionary was developed through a **researcher-controlled, iterative process** that combines lexical search with explicitly defined semantic categories:

1. **Define the concepts.** Establish inclusion and exclusion rules for each attribute. The semantic meaning of an attribute is defined independently of any particular phrase list.
2. **Discover candidate expressions.** Extract frequent unigrams and n-grams from the benefit-related vacancy sections. These expressions provide initial candidates; frequency-based seed discovery does **not** impose an absolute frequency threshold on the final dictionary.
3. **Create a semantic reference during development.** Use detailed category-specific coding instructions with **Qwen 3.8 27B**. For positive classifications, request the supporting text span.
4. **Refine expressions and boundaries.** Compare dictionary decisions to the semantic classification, inspect missed formulations and systematic false positives, and add, revise, or remove phrases as appropriate. The **F1 score** is the primary summary metric during refinement. ChatGPT was also used to assess semantic consistency of candidate expressions with the definitions.
5. **Freeze and validate.** Lock the final definitions and dictionary and assess them on a separate holdout sample that played no role in phrase selection, removal, or prompt refinement.

The development process uses language models as **research aids** for the lexical instrument. The production application of the finished dictionary does not require querying an LLM for each vacancy.

## Classification and aggregation rules

- **Text scope.** Apply the dictionary to extracted **benefit sections**, not indiscriminately to every word of the entire posting.
- **Positive and explicit claims.** An attribute must be explicitly offered or clearly described as an attractive working condition. Negation, job requirements, unrelated task descriptions, and mere statements of statutory minimum standards do not qualify.
- **Attribute-level indicator.** An attribute is coded as present if at least one qualifying expression for that attribute is matched under the specified text-matching procedure.
- **Group-level indicator.** A group is coded as present if at least one of its constituent attributes is present (logical **OR**).
- **Multiple attributes.** Vacancies can be positive for multiple attributes and multiple groups. A statement can contribute to two categories when it independently meets both definitions (e.g., *free parking* also denotes *parking*).
- **Excluded concepts.** The five categories under `excluded` may appear in the released dictionary, but **do not enter** the baseline any-benefit, group, or non-wage aggregates.
- **Interpretation.** A detected attribute is evidence that a vacancy **advertises** the characteristic; it is not proof of a contractual entitlement or an observed worker experience.

## Validation design and key findings

### Out-of-sample evaluation

After freezing the dictionary and category-specific instructions, both the dictionary and the final semantic reference procedure were applied independently to the **same 14,481 held-out advertisements**. The evaluation reports the correspondence between the deterministic dictionary and the reference classification, for each attribute and for group aggregates. The accompanying methods description specifies a frozen, model-based semantic reference procedure. The validation statistics should therefore be interpreted as agreement with that reference classification rather than as direct proof of real-world benefit provision.

For a given attribute, the reported metrics are:

- **Dictionary-positive share:** `(TP + FP) / N`, where `N = 14,481`.
- **Precision:** `TP / (TP + FP)`, i.e., the fraction of dictionary positives also positive in the reference classification.
- **Recall:** `TP / (TP + FN)`, i.e., the fraction of reference positives recovered by the dictionary.
- **F1:** `2 × precision × recall / (precision + recall)`.

Group-level metrics are calculated after aggregating constituent attributes with the logical OR rule; they are **not averages** of the individual attribute metrics. The positive share therefore need not equal the sum of individual shares.

### Interpretation of the findings

**Aggregate performance is high.** The nine group-level F1 scores range from **0.842** (workplace resources, health, and accessibility) to **0.962** (fringe benefits and perks). Group precision ranges from **0.881** to **0.972**.

**Concrete attributes are easier to recover.** Occupational pensions (F1 **0.974**), vacation of at least 30 days (**0.962**), permanent contracts (**0.960**), and bicycle schemes (**0.954**) are captured particularly well. Abstract attributes are harder to recognize using a fixed set of expressions: *meaningful work* has a recall of **0.371** (F1 **0.531**), while *workplace equipment* has a recall of **0.573** (F1 **0.682**).

**Precision and recall involve a trade-off.** The wordbook deliberately emphasizes interpretable, well-defined expressions over capturing every creative phrase an employer might use. As a result, non-detection should not be interpreted as definitive evidence that an amenity was not offered.

## Complete validation results

The table below reproduces the validation figures from the supplied LaTeX table. Within each dimension, individual attributes are ordered by their dictionary-positive share. Bold rows refer to a **group aggregate**; the remaining rows refer to individual attributes. Excluded concepts are shown separately and are not used in baseline analysis.

<details>
<summary><strong>Show complete validation table</strong> — 9 dimensions, 50 active attributes, 5 excluded concepts</summary>

| Dimension / attribute | Positive share | Precision | Recall | F1 |
|:--|--:|--:|--:|--:|
| **Pay & Monetary Compensation — group aggregate** | **57.1%** | **0.971** | **0.902** | **0.935** |
| &nbsp;&nbsp;Salary | 33.8% | 0.921 | 0.771 | 0.839 |
| &nbsp;&nbsp;Additional fixed payments | 20.2% | 0.972 | 0.876 | 0.922 |
| &nbsp;&nbsp;Collectively bargained pay | 18.3% | 0.958 | 0.864 | 0.908 |
| &nbsp;&nbsp;Bonuses | 12.0% | 0.829 | 0.734 | 0.778 |
| &nbsp;&nbsp;Allowances | 5.4% | 0.914 | 0.779 | 0.841 |
| &nbsp;&nbsp;Above-tariff pay | 3.6% | 0.819 | 0.830 | 0.824 |
| **Fringe Benefits & Perks — group aggregate** | **57.9%** | **0.972** | **0.952** | **0.962** |
| &nbsp;&nbsp;Occupational pension | 39.1% | 0.980 | 0.968 | 0.974 |
| &nbsp;&nbsp;Employee discounts | 20.5% | 0.924 | 0.762 | 0.835 |
| &nbsp;&nbsp;Sports and leisure benefits | 14.9% | 0.925 | 0.787 | 0.850 |
| &nbsp;&nbsp;Drinks | 10.8% | 0.964 | 0.927 | 0.945 |
| &nbsp;&nbsp;Food support / canteen | 10.7% | 0.918 | 0.844 | 0.879 |
| &nbsp;&nbsp;Capital-forming benefits | 9.5% | 0.985 | 0.952 | 0.968 |
| &nbsp;&nbsp;Snacks and fruit | 6.2% | 0.979 | 0.878 | 0.926 |
| **Working Time, Flexibility & Work-Life — group aggregate** | **59.0%** | **0.967** | **0.933** | **0.950** |
| &nbsp;&nbsp;Flexible working hours | 32.2% | 0.957 | 0.885 | 0.920 |
| &nbsp;&nbsp;At least 30 days of vacation | 22.1% | 0.959 | 0.965 | 0.962 |
| &nbsp;&nbsp;Remote work | 19.3% | 0.964 | 0.926 | 0.945 |
| &nbsp;&nbsp;Family and care benefits | 10.4% | 0.938 | 0.767 | 0.844 |
| &nbsp;&nbsp;Family-friendliness | 10.2% | 0.752 | 0.878 | 0.810 |
| &nbsp;&nbsp;Work-life balance | 9.2% | 0.835 | 0.679 | 0.749 |
| &nbsp;&nbsp;Additional leave | 6.7% | 0.915 | 0.642 | 0.755 |
| &nbsp;&nbsp;Part-time option | 5.4% | 0.918 | 0.779 | 0.843 |
| &nbsp;&nbsp;Predictable and regular working hours | 3.6% | 0.812 | 0.602 | 0.691 |
| &nbsp;&nbsp;Working week below 40 hours | 3.5% | 0.913 | 0.694 | 0.788 |
| **Employment Security — group aggregate** | **32.9%** | **0.934** | **0.914** | **0.924** |
| &nbsp;&nbsp;Permanent employment contract | 21.4% | 0.962 | 0.958 | 0.960 |
| &nbsp;&nbsp;Job security | 17.2% | 0.855 | 0.811 | 0.832 |
| **Career Development & Human Capital — group aggregate** | **69.1%** | **0.961** | **0.941** | **0.951** |
| &nbsp;&nbsp;Training | 48.5% | 0.957 | 0.911 | 0.934 |
| &nbsp;&nbsp;General development opportunities | 28.8% | 0.817 | 0.795 | 0.805 |
| &nbsp;&nbsp;Onboarding | 22.9% | 0.967 | 0.888 | 0.926 |
| &nbsp;&nbsp;Advancement opportunities | 9.1% | 0.904 | 0.658 | 0.762 |
| &nbsp;&nbsp;Coaching and mentoring | 8.0% | 0.905 | 0.742 | 0.815 |
| **Task Content, Autonomy & Meaning — group aggregate** | **39.8%** | **0.881** | **0.814** | **0.846** |
| &nbsp;&nbsp;Interesting and varied tasks | 29.5% | 0.823 | 0.814 | 0.818 |
| &nbsp;&nbsp;Autonomy and discretion | 18.0% | 0.891 | 0.706 | 0.788 |
| &nbsp;&nbsp;Meaningful work | 1.1% | 0.930 | 0.371 | 0.531 |
| **Social Environment & Organizational Culture — group aggregate** | **58.9%** | **0.944** | **0.881** | **0.911** |
| &nbsp;&nbsp;Organizational culture and atmosphere | 32.4% | 0.875 | 0.788 | 0.829 |
| &nbsp;&nbsp;Team quality | 22.5% | 0.892 | 0.669 | 0.765 |
| &nbsp;&nbsp;Flat hierarchies | 17.9% | 0.956 | 0.920 | 0.938 |
| &nbsp;&nbsp;Company events | 16.5% | 0.947 | 0.876 | 0.910 |
| &nbsp;&nbsp;Diversity and inclusion | 4.2% | 0.722 | 0.699 | 0.710 |
| **Workplace Resources, Health & Accessibility — group aggregate** | **25.6%** | **0.922** | **0.775** | **0.842** |
| &nbsp;&nbsp;Health management | 17.3% | 0.910 | 0.836 | 0.871 |
| &nbsp;&nbsp;Workplace equipment | 7.8% | 0.841 | 0.573 | 0.682 |
| &nbsp;&nbsp;Employer-provided digital devices | 4.0% | 0.938 | 0.761 | 0.840 |
| &nbsp;&nbsp;Workplace accessibility | 0.4% | 0.952 | 0.952 | 0.952 |
| **Location, Commuting & Mobility Benefits — group aggregate** | **39.4%** | **0.965** | **0.904** | **0.933** |
| &nbsp;&nbsp;Bicycle scheme | 19.3% | 0.956 | 0.953 | 0.954 |
| &nbsp;&nbsp;Public-transport ticket | 11.3% | 0.960 | 0.841 | 0.897 |
| &nbsp;&nbsp;Transport accessibility | 7.3% | 0.858 | 0.751 | 0.801 |
| &nbsp;&nbsp;Parking | 7.3% | 0.975 | 0.865 | 0.917 |
| &nbsp;&nbsp;Company car | 5.5% | 0.951 | 0.870 | 0.909 |
| &nbsp;&nbsp;Mobility subsidy | 4.0% | 0.726 | 0.649 | 0.685 |
| &nbsp;&nbsp;Free parking | 4.0% | 0.959 | 0.832 | 0.891 |
| &nbsp;&nbsp;Housing and relocation support | 2.1% | 0.897 | 0.626 | 0.737 |
| **Excluded concepts (not in baseline)** | — | — | — | — |
| &nbsp;&nbsp;Generic social benefits | 9.5% | 0.741 | 0.890 | 0.809 |
| &nbsp;&nbsp;Insurance benefits | 4.8% | 0.541 | 0.668 | 0.598 |
| &nbsp;&nbsp;Protective equipment | 4.2% | 0.959 | 0.921 | 0.939 |
| &nbsp;&nbsp;International work environment | 3.4% | 0.812 | 0.526 | 0.639 |
| &nbsp;&nbsp;Co-determination | 0.6% | 0.778 | 0.733 | 0.754 |

</details>

**Table note.** Positive share is the share of the validation sample identified as positive by the **dictionary**, `(TP + FP) / N` with `N = 14,481`. Precision, recall, and F1 are evaluated relative to the reference classification. Group-level indicators equal one if any constituent attribute is present. Excluded concepts are reported for transparency only.

## Scope, limitations, and reproducibility

- **Advertised versus realized amenities.** Vacancy statements may convey promises, employer branding, or signals about unobserved job characteristics; the dictionary does not directly measure realized working conditions or worker satisfaction.
- **Language and domain.** The expression lists were developed for **German-language online job advertisements**, specifically their benefit sections. Performance may differ for other languages, time periods, sources, or when applied to complete vacancy descriptions.
- **Potential false negatives.** Rare, implicit, or unusually phrased benefits may not be captured. Precision and recall also vary meaningfully across attributes.
- **Lexical dependencies.** Output depends on text preprocessing, normalization, expression matching, and section extraction. The wordbook files alone do not define an end-to-end matching algorithm.
- **Group boundaries.** The company-car attribute is classified under mobility in the baseline; robustness checks can move it to fringe benefits without changing its underlying matches.
- **Reproducibility.** Keep the JSON/CSV dictionary, category definitions, zoning and matching code, and a version identifier together. Avoid changing phrase lists after consulting the held-out validation sample if reporting the same out-of-sample results.

**Research use.** This repository documents a measurement tool for studying the **advertised** bundle of job attributes in vacancy data. When adapting it to a new corpus, validate the frozen dictionary on an independent sample from that corpus and report changes in its precision and recall.
