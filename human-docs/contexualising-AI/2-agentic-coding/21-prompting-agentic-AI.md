# Agentic AI coder ESSENTIAL: sensible prompting

## Plan first. Build second.

- planning drastically improves efficiency
  - implementation plan
  - dependency map
  - file‑level breakdown
  - test strategy

## Don't embed everything into prompts

- refer to external documentations

```
## API Documentation
See OpenAPI spec at `/docs/api/openapi.yaml`

## Design System
Refer to Figma: [link] or Storybook at `/storybook`

## Architecture Decisions
See ADRs in `/docs/adr/` directory

## Component Patterns
Browse component library at `/docs/components.md`
```

> Of course, make sure MCP is setup correctly for your AI friend to access those documentations !

## Structure requests

### implement feature

```
1. Context: "I'm implementing [feature] in [location]"
2. Requirements: "It should [specific behaviors]"
3. Constraints: "Follow patterns from [reference file]"
4. Validation: "Include tests for [scenarios]"
5. Success Criteria: "Done when [measurable outcomes]"
```

- example

```
I'm implementing user avatar upload in src/components/Profile/AvatarUpload.tsx

Requirements:
- Support PNG, JPEG, WebP (max 5MB)
- Preview before upload
- Crop to square aspect ratio
- Upload to S3 with signed URLs

Constraints:
- Follow form pattern from src/components/forms/ImageUploadForm.tsx
- Use upload utilities from src/lib/upload/s3-client.ts
- Match existing profile component styling

Validation:
- Test file type validation
- Test file size limits
- Test upload error handling
- Test preview rendering

Done when:
- Component renders with preview
- Files upload successfully to S3
- Error states handled gracefully
- Tests passing with 90%+ coverage
```

### debug

```
1. Observation: "What I'm seeing: [actual behavior]"
2. Expectation: "What should happen: [expected behavior]"
3. Context: "Relevant code: [file paths or snippets]"
4. Investigation: "What I've tried: [debugging steps]"
5. Question: "Where should I look next?"
```

- example

```
Observation: Timer shows NaN after 5 minutes running

Expectation: Should show remaining time in MM:SS format

Context:
- Timer component: src/components/Timer/TimerWidget.tsx
- Timer logic: src/lib/timer/timer-manager.ts
- Using interval: setInterval(() => tick(), 1000)

Investigation:
- Console shows remainingSeconds becomes NaN
- Happens only after 5+ minutes
- Not reproducible in development, only production

Where should I look next?
```

### Review

```
Before we proceed, please review the code you just generated:

1. Security: Are all inputs validated? Any injection risks?
2. Performance: Any O(n²) operations? Unnecessary re-renders?
3. Error Handling: What happens if [edge case]?
4. Testing: What test cases should cover this?
5. Compliance: Does it follow our AGENTS.md rules?

List any concerns and suggest improvements.
```
