# stablephone skill — Scratchpad

## What works well
- Simple trigger flow (POST call → poll status → read transcript)
- Voice options table is handy
- Optional fields table covers all customization

## Potential improvements
- Could add example task prompts that produce good calls
- Transfer flow (transfer_phone_number) could use more detail
- Could add guidance on writing effective task instructions
- Missing: how call pricing works internally (flat $0.54 regardless of duration?)
- Could add a "voicemail strategies" section (when to use each action)
- Phone number buying is expensive ($20) — should note when it's worth it vs. using random caller ID
- Could add references/ file with example call transcripts showing good vs. bad task descriptions
- US/CA only — should note geographic limitations
- Missing: what happens if the call fails (busy, no answer, etc.)

## Missing
- Supported countries beyond US/CA
- Call recording format and access
- How long recordings/transcripts are retained
- Whether calls can be monitored in real-time
- Custom voice support beyond presets

## Optimization ideas
- Script to make a call and wait for completion, returning transcript
- Batch calling with status tracking
- Integration with StableEnrich (look up person → call them)
