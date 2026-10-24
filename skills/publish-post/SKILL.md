---
name: publish-post
description: Preview and then publish or schedule a LinkedIn post through Content Mill. Use when the user wants to publish, post now, schedule or send a LinkedIn post.
user-invocable: true
---

# /content-mill-linkedin:publish-post

Publishing is two steps, and the second only happens after the user has seen the first and said yes.

Arguments passed: `$ARGUMENTS` (the post, or the id of a draft to publish; ask if it is empty).

## Steps

1. `list_social_accounts`: use only Connected accounts; ask which LinkedIn account if it is unclear.
2. `get_platform_limits`, then write or adjust the post to fit. A LinkedIn post is at most 3,000 characters, with up to 9 images or one video (never mixed); media is optional.
3. Media, if any: `upload_media_from_url` (public https link) or `create_media_upload` then `finish_media_upload`. Never put file contents in a tool argument.
4. `preview_post` with the exact `content`, `account_ids`, `media_urls`, and `scheduled_at` if the user wants it later (ISO 8601 with a timezone offset, in the future; ask if the timezone is unknown). Omit `scheduled_at` to publish as soon as it is confirmed.
5. **Show the user the preview** (text, account, time) and **wait for their approval.** Never call `publish_post` without showing the preview, and never set `confirmed` on their behalf.
6. `publish_post` with the `preview_token` and `confirmed: true`. A preview is single-use, expires after 10 minutes and publishes exactly what was previewed; to change anything, preview again.
7. Publishing is asynchronous: call `get_post` afterwards and report the result and the link to the live post.

A scheduled post cannot be edited. To change one, remove it with `delete_post` and create it again through a new preview.
