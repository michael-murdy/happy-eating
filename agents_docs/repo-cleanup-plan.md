# Repository Cleanup & Image Rotation Fix Plan

**Date:** 2025-01-18
**Status:** Ready for review
**Prepared by:** Claude (Architect)
**Implementer:** OpenAI Codex

---

## Executive Summary

This document outlines cleanup tasks and the image rotation fix for the Happy Eating recipe blog. The repo has accumulated structural inconsistencies and the current image rotation implementation uses CSS transforms which don't properly handle the rotated image dimensions. The fix requires switching to Hugo's native `images.Rotate` filter.

---

## Part 1: Repository Cleanup

### 1.1 Fix Empty `.gitignore` (Critical)

**Problem:** The `.gitignore` file is empty. Build artifacts are being committed, bloating the repository by ~53MB.

**Task for Codex:**
```
Update .gitignore with:

public/
resources/_gen/
.hugo_build.lock
.DS_Store
```

After updating `.gitignore`, remove the cached files from git tracking:
```bash
git rm -r --cached public/
git rm -r --cached resources/_gen/
```

---

### 1.2 Rename Recipe Directories to Use Hyphens (Medium)

**Problem:** Recipe directories use underscores but AGENTS.md specifies hyphenated slugs.

| Current | Should Be |
|---------|-----------|
| `content/recipes/hogfish_grilled_on_citrus/` | `content/recipes/hogfish-grilled-on-citrus/` |
| `content/recipes/tenderloin_with_mushroom_wine/` | `content/recipes/tenderloin-with-mushroom-wine/` |

**Task for Codex:**
1. Rename both directories
2. Verify `hugo server -D` builds without errors
3. Confirm URLs resolve correctly

---

### 1.3 Fix Tenderloin Recipe Image Path (Medium)

**Problem:** The tenderloin recipe references an absolute path that bypasses page bundles.

**File:** `content/recipes/tenderloin_with_mushroom_wine/index.md` (or renamed path)

**Current:**
```yaml
featured_image: "/images/pork_1.jpeg"
```

**Should be:**
```yaml
featured_image: "pork_1.jpeg"
```

The image `pork_1.jpeg` already exists in the page bundle directory.

---

### 1.4 Update Archetype to Match Actual Usage (Low)

**Problem:** The archetype template uses `image:` but all recipes use `featured_image:`.

**File:** `archetypes/recipes.md`

**Changes:**
- Replace `image: ""` with `featured_image: ""`
- Add `featured_image_angle: 0`
- Remove unused `no_prep_rest_time:` field (or keep if planning to use it)

**Proposed archetype:**
```yaml
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
categories: []
tags: []
featured_image: ""
featured_image_angle: 0
prep_time: ""
cook_time: ""
servings: ""
---
## Ingredients
-

## Instructions
1.
```

---

### 1.5 Stage Layout Directory Properly (Low)

**Problem:** Git shows layout files as untracked due to directory staging issues.

**Task for Codex:**
```bash
git add layouts/
```

This will properly track all files under `layouts/`.

---

## Part 2: Image Rotation Fix

### 2.1 Problem Analysis

**Current implementation:** CSS `transform: rotate()` on the `<img>` element.

**Why it doesn't work:**
1. A 90° rotation on a landscape image creates a portrait orientation, but the container still has landscape dimensions
2. `overflow: hidden` clips the rotated image corners
3. The image doesn't fill its container properly after rotation
4. Responsive sizing breaks because the aspect ratio changes

**Example:** A 1600x1200 image rotated 90° becomes effectively 1200x1600, but the CSS container still expects 1600x1200.

### 2.2 Solution: Use Hugo's `images.Rotate` Filter

Hugo's image processing can rotate the actual image data, producing a correctly-oriented output file.

**Reference:** The existing `layouts/shortcodes/rotated.html` already does this correctly:
```go
{{- $processed = $processed | images.Rotate $angle -}}
```

### 2.3 Implementation Plan

#### Step 1: Update `layouts/partials/func/GetFeaturedImage.html`

Add image rotation processing when `featured_image_angle` is non-zero.

**Current behavior:** Returns the image URL and stores the resource in Scratch.

**New behavior:** If angle is specified, rotate the image resource using Hugo's `images.Rotate` filter before storing in Scratch.

**Pseudocode:**
```go
{{- $angle := int (default 0 $page.Params.featured_image_angle) -}}

{{- with $page.Resources.GetMatch $candidate -}}
  {{- $img := . -}}
  {{- if ne $angle 0 -}}
    {{- $img = $img | images.Rotate $angle -}}
  {{- end -}}
  {{- $page.Scratch.Set "featured_image_resource" $img -}}
{{- end -}}
```

#### Step 2: Remove CSS Rotation from `layouts/partials/featured-image.html`

**Remove:** The `$style` variable and CSS transform logic (lines 4-8, and style attributes).

**Keep:** The responsive `<picture>` element with multiple sizes.

**Rationale:** Once the image resource is pre-rotated, CSS transforms are unnecessary.

#### Step 3: Update `layouts/partials/site-header.html`

**Current:** Two code paths—one for angle=0 (background-image) and one for angle≠0 (img with CSS rotation).

**New approach:**
- Always use the rotated image resource from Scratch
- Can use either background-image or `<img>` since the image is already correctly oriented
- Simplify to single code path

**Key change:** Replace the CSS rotation approach (lines 23-40) with pre-rotated image usage.

#### Step 4: Verify Shortcode Still Works

The `layouts/shortcodes/rotated.html` already uses `images.Rotate` correctly. No changes needed, but verify it still works after the other changes.

### 2.4 Test Cases

1. **Hogfish recipe** (`featured_image_angle: 90`) - Verify image displays correctly rotated
2. **Tenderloin recipe** (no angle or angle: 0) - Verify no rotation applied
3. **New recipe** with 180° rotation - Test other angles work
4. **Inline shortcode** - Test `{{< rotated src="img.jpg" angle="90" >}}` still works
5. **Responsive images** - Verify all three sizes (800, 1200, 1600) are rotated

### 2.5 Files to Modify

| File | Change Type |
|------|-------------|
| `layouts/partials/func/GetFeaturedImage.html` | Add rotation processing |
| `layouts/partials/featured-image.html` | Remove CSS rotation |
| `layouts/partials/site-header.html` | Simplify to use pre-rotated image |

---

## Part 3: Optional Improvements

These are lower priority and can be addressed later.

### 3.1 Delete Custom `single.html` or Complete It

**Problem:** `layouts/_default/single.html` is a minimal 10-line template that doesn't match the theme's styling (missing Tachyons classes, sidebar, tags, etc.).

**Options:**
- **Option A:** Delete it and use theme's default (loses featured-image partial)
- **Option B:** Expand it to include full theme styling with rotation support

**Recommendation:** Option B if you want the custom featured-image display; otherwise Option A.

### 3.2 Add Recipe Description Field

Neither recipe has a `description:` field. Adding one would:
- Improve SEO (meta description)
- Display subtitle in page header
- Show in recipe cards/listings

---

## Task Assignment Summary

| Priority | Task | Section |
|----------|------|---------|
| Critical | Update `.gitignore` and remove cached files | 1.1 |
| High | Implement image rotation fix | 2.3 |
| Medium | Rename recipe directories to hyphens | 1.2 |
| Medium | Fix tenderloin image path | 1.3 |
| Low | Update archetype | 1.4 |
| Low | Stage layouts directory | 1.5 |

---

## Verification Checklist

After all changes:
- [ ] `hugo` builds with no warnings
- [ ] `hugo server -D` runs successfully
- [ ] Hogfish recipe shows rotated featured image (correctly oriented)
- [ ] Tenderloin recipe shows unrotated featured image
- [ ] Recipe URLs use hyphenated slugs
- [ ] `git status` shows no untracked files in `public/` or `resources/`
- [ ] All images load (no 404s)
