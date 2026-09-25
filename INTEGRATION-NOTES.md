# Education Resource Data — integration notes

This tool uses the same browser-storage strategy as the existing Education Experience Bank, but keeps school/position research in a separate shared store.

## Shared key

`uiowaEducationResourceData_v1`

The School Research tool writes:

- `schoolResearch.current`
- `schoolResearch.saved`
- `handoffs.tailoring`
- `handoffs.coverLetter`
- `handoffs.interview`
- `handoffs.studentTeaching`

All tools that need to share data must run on the **same web origin/domain**. For example, GitHub Pages pages under the same site can share `localStorage`. An ICON page and an embedded GitHub page do not share the same storage directly, but multiple tools served from the same GitHub Pages origin do.

## Recommended setup

Keep `education-resource-data.js` in the same folder/site as the Education quick tools and include this before each tool's own JavaScript:

```html
<script src="education-resource-data.js"></script>
```

Then a cover-letter activity can read the latest school research with:

```js
const school = EducationResourceData.get('handoffs.coverLetter');

if (school) {
  console.log(school.schoolName);
  console.log(school.schoolConnection);
  console.log(school.evidence);
  console.log(school.emphasis);
}
```

An interview activity can use:

```js
const prep = EducationResourceData.get('handoffs.interview');
```

An application-tailoring activity can use:

```js
const prep = EducationResourceData.get('handoffs.tailoring');
```

Student-teaching prep can use:

```js
const prep = EducationResourceData.get('handoffs.studentTeaching');
```

## Student-facing import pattern

Do not silently insert saved responses into a new activity. A better interaction is:

> We found school research you already completed for Lincoln Elementary.
> **Use my saved school research**

Then fill the appropriate fields only after the student chooses to use it.

Example:

```js
const saved = EducationResourceData.get('handoffs.coverLetter');

if (saved && saved.schoolName) {
  importButton.hidden = false;
  importButton.textContent = `Use my ${saved.schoolName} research`;

  importButton.addEventListener('click', () => {
    schoolNameInput.value = saved.schoolName || '';
    priorityInput.value = saved.schoolConnection || '';
    evidenceInput.value = saved.evidence || '';
  });
}
```

## Existing Education Experience Bank

The school research tool also checks for:

`uiowaEducationExperienceBank_v1`

It does not change or delete that data. If it finds the existing experience bank, it gives the student a small prompt reminding them that one of their saved experiences may provide evidence for the school connection.

## Suggested future shared structure

Use `uiowaEducationResourceData_v1` for cross-tool information that is not specifically a resume experience, such as:

- selected school/district
- target role
- school priorities
- school-specific evidence/connection
- cover-letter planning
- interview talking points/questions
- tailoring decisions

Continue to use `uiowaEducationExperienceBank_v1` for experiences, skills, and resume bullets.
