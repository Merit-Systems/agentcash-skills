# stableemail skill — Scratchpad

## What works well
- Three-tier structure (shared/inbox/subdomain) clearly laid out
- Comprehensive endpoint tables with pricing
- Tips section covers common gotchas (image hosting, message retention)

## Potential improvements
- Could add example request/response bodies for key endpoints (send, buy inbox, buy subdomain)
- The attachment format (base64 + contentType + filename) could use a concrete example
- Could add a "choosing the right tier" decision tree
- Subdomain DNS verification process could be more detailed
- Message reading/pagination not documented (cursor-based?)
- Could add references/ file with HTML email templates
- SIWX auth flow for free endpoints could be clarified
- Catch-all forwarding on subdomains is a powerful feature that deserves more attention
- Pro-rata refund calculation on cancel is unclear (how is it computed?)

## Missing
- Email size limits (besides attachment limits)
- Bounce/delivery status tracking
- Rate limiting on sends
- Supported HTML/CSS in emails (email client compatibility)
- How to test emails without sending to real recipients

## Optimization ideas
- Script to create a full subdomain setup (buy → wait for DNS → create inboxes)
- Template system for common email formats (welcome, notification, newsletter)
- Integration with StableUpload for image-heavy emails
