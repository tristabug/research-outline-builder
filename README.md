# research-outline-builder
A tool for researchers to plan papers before writing, build a reorder-able outline and attach sources to each section as you find them.

## The Problem

Researchers collect sources way before they start writing, and it's easy to lose track of which source goes where, especially since the structure of the paper is usually still changing at that point. Sources live in one tool, the outline lives in your head or a doc somewhere, and the draft lives somewhere else entirely, with nothing tying them together.

## Core Functionality

- Build an outline made up of sections (Introduction, Literature Review, Methodology, Results, Discussion, Conclusion, or custom ones)
- Reorder sections at any time as the structure of the paper changes
- Keep sources in a shared Resource Library and attach them to sections as you find them. The same resource can be used in many sections and outlines. A resource can be:
  - A journal article
  - A news article
  - Media (image, video, dataset, etc.)
  - A custom placeholder for something you haven't sourced yet (e.g. "need a bar chart comparing X and Y")
- Archive resources you no longer need instead of deleting them. Archived resources stay in outlines that already use them and can be restored at any time.
- See every change made to a resource in its change log, including which outlines and sections were affected

## Data Model

Three main pieces:

- **Outline** – the paper itself
- **Section** – ordered pieces of the outline, each with the resources attached to it
- **Resource** – a source in the Resource Library, with a type field (journal article, news article, media, or placeholder), since each type needs different info attached (author/year/DOI for a journal article vs. a short note for a placeholder). A resource can be attached to many sections.

Resources also keep a change log. The full tables and rules are in [Pages & Data](#pages--data) below.

## Interface

The main screen is the outline view: a reorderable list of sections you can open and edit, with resources for each section listed underneath. Adding a resource to a section lets you pick one from the Resource Library or create a new one. Creating a resource opens a form where you pick the type first, then fill in the fields that make sense for that type. Every page is described in [Pages & Data](#pages--data) below.

## Planned Integrations (stretch goals)

- **Mendeley** – pull sources directly from an existing reference library instead of typing them in manually
- **Word export** – export the outline into a Word doc as a starting draft, with sections and resource notes already laid out

## Similar Tools

Zotero and Mendeley manage sources but aren't built around an outline. Scrivener has an outline view but doesn't track sources by section or connect to a reference manager. This project sits in the middle: outline first, sources attached at the section level, eventually linked to the tools people already use.

## Pages & Data

This section plans out what pages the app has, what the user enters and sees on each one, and what gets stored. The first version is a single-user app with no login.

Resources live in a shared **Resource Library**, separate from any one outline. The same resource can be attached to many sections, across any number of outlines. Resources are never deleted. Instead they can be archived, which keeps them in every outline that already uses them but takes them off the list of resources you can add to a section. An archived resource can be restored at any time. Resources can be edited, and edits show up everywhere the resource is used. Every change is recorded in the resource's change log.

### Pages

**1. My Outlines (home page)**
- **Shows:** a list of your outlines with the title, description, number of sections, and when each one was last updated
- **User can:** open an outline, create a new one, edit or delete an existing one, go to the Resource Library

**2. Create / Edit Outline**
- **Inputs:** Title (required), Description (optional)
- **After saving:** goes to the Outline View for that outline

**3. Outline View (main screen)**
- **Shows:** the outline's sections in order, with the resources attached to each section listed underneath it
- **User can:**
  - move a section up or down with arrow buttons
  - add, edit, or delete a section
  - add a resource to a section
  - edit a resource's notes for that section
  - remove a resource from a section (this only removes it from that section; the resource stays in the Resource Library)
- Each resource shows its type, its title, a short summary (authors and year for a journal article, for example), and any notes for that section
- Archived resources still show up in sections they were added to before they were archived, with an "Archived" label

**4. Add / Edit Section**
- **Inputs:**
  - Section name, picked from a dropdown (Introduction, Literature Review, Methodology, Results, Discussion, Conclusion) or typed in as a custom name
  - Notes (optional)
- New sections are added at the bottom of the outline and can be moved from there

**5. Add Resource to Section**
- **Shows:** the active (not archived) resources in the library, which can be filtered by type or searched by title. Resources already in this section aren't listed.
- **Inputs:**
  - the resource to add
  - Notes for this section (optional), for how you plan to use the resource in this part of the paper
- **User can also:** create a new resource (goes to pages 8 and 9, then comes back here with the new resource selected)
- **After saving:** goes back to the Outline View with the resource listed under its section

**6. Resource Library**
- **Shows:** every resource with its type, title, the start of its description, and how many sections use it. A filter switches between active and archived resources.
- **User can:** create, view, or edit a resource, archive an active resource, and restore an archived one. There is no delete option.
- Restoring happens right away with a Restore button (no confirmation page), since it doesn't remove anything

**7. Resource Details**
- **Shows:**
  - all of the resource's fields, and whether it's archived
  - the sections that use it, grouped by outline, with links to each outline
  - the change log: a list of every change, newest first. Each entry shows:
    - the date and what happened (created, edited, archived, restored)
    - for edits, each changed field's old and new value
    - the sections affected by the change, grouped by outline. Outlines and sections that still exist link to the Outline View; deleted ones show their old name with "(deleted)"
- **User can:** edit, archive, or restore the resource

**8. Create Resource: Step 1, Choose Type**
- **Inputs:** resource type (Journal Article, News Article, Media, or Placeholder)
- Leads to the form for that type

**9. Create / Edit Resource: Step 2, Enter Fields**
- **Inputs:** Title, Description, and the fields for the chosen type (see the Resource fields below)
- Editing a resource updates it everywhere it's used, and each changed field is added to the change log
- The resource type can't be changed after the resource is created
- **After saving:** goes back to wherever the user came from (the Resource Library, Resource Details, or Add Resource to Section)

**10. Archive Resource Confirmation**
- Explains that the resource will stay in every section it's already in, but can't be added to any new sections until it's restored
- **After archiving:** goes back to the Resource Library

**11. Delete Confirmation** (outline or section)
- Asks "Are you sure?" before deleting. Deleting a section removes its links to resources, and deleting an outline removes its sections and their links. The resources themselves are never deleted and stay in the Resource Library.

### Data

**Outline**
| Field | Type | Rules |
|---|---|---|
| OutlineId | int | primary key |
| Title | string | required, max 200 characters |
| Description | string | optional |
| DateCreated | DateTime | set automatically |
| DateModified | DateTime | updated whenever anything in the outline changes |

**Section**
| Field | Type | Rules |
|---|---|---|
| SectionId | int | primary key |
| OutlineId | int | foreign key to Outline |
| Name | string | required, max 100 characters |
| Notes | string | optional |
| Position | int | the section's order in the outline (1, 2, 3...) |

**Resource** (fields all types share)
| Field | Type | Rules |
|---|---|---|
| ResourceId | int | primary key |
| ResourceType | enum | Journal Article, News Article, Media, or Placeholder |
| Title | string | required |
| Description | string | required, max 2,000 characters; a brief description of the resource (around a paragraph) that isn't tied to any outline or section |
| IsArchived | bool | defaults to false; archived resources can't be added to sections until restored |
| DateCreated | DateTime | set automatically |
| DateArchived | DateTime? | set when the resource is archived, cleared when it's restored |

**Extra fields for each resource type**
| Type | Fields |
|---|---|
| Journal Article | Authors (required), Year, Journal Name, Volume, Issue, Pages, DOI, URL |
| News Article | Author(s), Publication (required), Date Published, URL |
| Media | Media Kind (image, video, dataset, other), Creator, Source / Publisher, Year, URL |
| Placeholder | no extra fields; the Title is the idea (e.g. "need a bar chart comparing X and Y") and the Description holds the details |

**SectionResource** (links a resource to a section)
| Field | Type | Rules |
|---|---|---|
| SectionResourceId | int | primary key |
| SectionId | int | foreign key to Section |
| ResourceId | int | foreign key to Resource |
| Notes | string | optional; how the resource will be used in this section |
| DateAdded | DateTime | set automatically; resources in a section are listed in the order they were added |

The same resource can't be added to the same section twice (SectionId and ResourceId together must be unique).

**ResourceChange** (the change log, one row per change)
| Field | Type | Rules |
|---|---|---|
| ResourceChangeId | int | primary key |
| ResourceId | int | foreign key to Resource |
| DateChanged | DateTime | set automatically |
| Action | enum | Created, Edited, Archived, or Restored |

**ResourceChangeField** (the fields changed by an edit)
| Field | Type | Rules |
|---|---|---|
| ResourceChangeFieldId | int | primary key |
| ResourceChangeId | int | foreign key to ResourceChange |
| FieldName | string | required; the field that changed |
| OldValue | string | the field's value before the edit |
| NewValue | string | the field's value after the edit |

One edit that changes three fields adds one Edited ResourceChange with three ResourceChangeField rows. Fields that didn't change aren't logged. Only Edited changes have field rows.

**ResourceChangeUsage** (the sections the resource was in when the change happened)
| Field | Type | Rules |
|---|---|---|
| ResourceChangeUsageId | int | primary key |
| ResourceChangeId | int | foreign key to ResourceChange |
| OutlineId | int? | the outline the section belonged to; cleared if the outline is later deleted |
| OutlineTitle | string | required; the outline's title at the time of the change |
| SectionId | int? | the affected section; cleared if the section is later deleted |
| SectionName | string | required; the section's name at the time of the change |

When a change is logged, one ResourceChangeUsage row is added for every section the resource is in at that moment. The outline title and section name are copied in, so the log still shows what was affected even if an outline or section is renamed or deleted later. A Created change usually has no usage rows, because a new resource isn't in any sections yet.

**Relationships**
- An Outline has many Sections.
- A Section has many Resources, and a Resource can be in many Sections. The SectionResource table connects them, which makes this a many-to-many relationship.
- A Resource has many ResourceChanges.
- A ResourceChange has many ResourceChangeFields (for edits) and many ResourceChangeUsages.

**Edit, delete and archive rules**
- Creating, editing, archiving and restoring a resource each add a ResourceChange. An edit also records each changed field, and every change records the sections the resource was in at that moment.
- Editing a resource updates it everywhere it's used.
- The change log can only be added to. Its rows (including field and usage rows) can't be edited or deleted. Deleting an outline or section only clears the OutlineId or SectionId on its usage rows; the copied title and name stay.
- Resources are never deleted. The app has no delete option for them, and the database is set up so that deleting a section or outline can't remove a resource row.
- Deleting a section deletes its SectionResource links. Deleting an outline deletes its sections and their links.
- Archiving a resource sets IsArchived to true. Its existing SectionResource links stay as they are, so outlines that already use it don't change.
- Archived resources are left out of the list on the Add Resource to Section page.
- Restoring a resource sets IsArchived back to false and clears DateArchived. It can then be added to sections again, and its existing links are unchanged.

**Storage plan:** all four resource types go in one Resources table, with a column that records each row's type. Entity Framework Core supports this directly (it's called table-per-hierarchy). The SectionResource table connects sections and resources, and an outline can be loaded with all its sections and their resources in one query.

**Validation:**
- Description is required
- Year must be between 1800 and the current year
- URL fields must be valid URLs
- DOI must start with `10.`

### Not in the first version
- Mendeley import and Word export (stretch goals, listed under Planned Integrations above)
- Drag-and-drop reordering (arrow buttons come first)
- User accounts and login

## Status

Planning stage. The pages and data model are planned out (see Pages & Data above). Wireframes come next, then the core outline and resource features. Integrations later in the semester.

