# PROMPTS.md: Living Prompt Pack

> Module 3 · Prompt Chaining. Re-architect the build with prompt chains; capture the reusable ones here.

## How to use this pack

_Each prompt is a reusable step. Chain them: the output of one becomes the input to the next._

## Prompt chain: realy-carla

### Step 1: Expand the existing onboarding journey by adding the missing transition states around the first-value and team-adoption moments.
```
Build the next phase of the Retention Engine in a strict sequence:
1. Keep the existing Welcome, Minimum Setup, First Value, Team Invitation, Activation Complete, and Experiment Results screens. Do not rebuild or remove existing screens.
2. Add a transition state after the user creates the weekly operations brief, clearly confirming that first value has been reached.
3. Add a transition into Team Adoption that explains that the next step is to turn individual value into shared team value by inviting at least two teammates.
4. Navigation: connect these new states to the existing flow so the journey moves logically from First Value → First Value confirmation → Team Adoption → Activation Complete.
Build these in order. Preserve the existing Retention Engine logic, content, data, and visual language.
```

### Step 2: Make the onboarding journey resilient by defining loading, empty, validation, and error states for brief generation and teammate invitations.
```
Apply the following logic constraints to the First Value and Team Invitation flows:
- While the weekly operations brief is being generated, show a loading state with the exact message: “Creating your weekly brief…”
- If the brief cannot be generated, show the error state: “We couldn't generate your brief. Try again.” Include a Retry action.
- If no teammates have been added yet, show the empty state: “Your brief is ready. Invite 2 teammates to turn this into shared team value.”
- If an invitation cannot be sent, show the error state: “Invite couldn't be sent. Check the email and try again.” Include a Retry action.
- Require at least two valid teammate email addresses before the user can complete activation.
Maintain the same design language throughout and preserve all existing working behavior.
```

### Step 3: Refine the First Value screen to make the “aha moment” visually clear and immediately understandable.
```
The First Value screen needs a professional B2B SaaS polish, using the attached screenshot as the visual reference.
1. First, audit the current First Value screen and identify the 3 biggest gaps in visual hierarchy, typography, and spacing compared to the attached reference screenshot.
2. Use the attached reference to improve the visual hierarchy, typography, spacing, card structure, and information density.
3. Make the generated weekly operations brief the clear visual focus of the screen, while maintaining the existing Relay visual identity.
4. Make the main blocker, recommended next action, and value delivered understandable at a glance.
Preserve all existing content, data, navigation, and functionality.
Do not change anything else in the project or touch the underlying logic.
```

## Reusable techniques learned

- _____
- _____

## What broke (and the fix)

_Where a single mega-prompt failed and chaining fixed it._

_____
