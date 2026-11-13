# Lab 4 WebSocket -- Project Report

## What I specified

**Sent sentence:** `you're right`
**Expected answer:** `We were discussing you, not me.`

## What I changed

I modified `src/test/kotlin/websockets/ElizaServerTest.kt` to complete the integration test:
* Removed `@Disabled` from `onChat` and `@Suppress("UNUSED_PARAMETER")` from `ComplexClient.onMessage`.
* Instructed `ComplexClient` to send the specified sentence immediately when `list.size == 3` (right after receiving the three welcome messages).
* In `onChat`, I added logic to store the `list.size` value in an immutable variable exactly when the `CountDownLatch` unblocks. 
* Checked `list[3]` with `assertEquals` for the exact string match of the DOCTOR's reply.

## Technical decisions
git add .
To write a reliable automated test, I needed an input that causes a deterministic 1:1 mapping in the DOCTOR script. I performed black-box testing on the endpoint using a browser WebSocket client extension. I rejected words like "hello", "school", "sad", "family", "miss my home", or "miss my friends" because my empirical tests proved they generate randomized responses, which would result in flaky tests that fail intermittently. I selected the sentence "you're right" because my tests demonstrated it consistently forces the bot into a specific defensive fallback ("We were discussing you, not me.") 100% of the time, guaranteeing assertion stability across all executions.

## How I verified

I ran `./gradlew check`. Initially, the test failed intermittently when asserting the exact size of the list (e.g., `assertEquals(4, size)`). This happened because the server sends a 5th message (the "---" separator) immediately after the 4th (the actual reply), causing a race condition where the list size could be either 4 or 5 depending on the thread scheduler. I fixed this by checking a safe interval (`assertTrue(size in 4..5)`). 
Later, `./gradlew check` failed again due to `ktlintTestSourceSetCheck` finding trailing spaces and unused imports. I fixed this by running `./gradlew ktlintFormat` to automatically fix the style violations, and finally ran `./gradlew check` again to ensure a fully green build.

## AI disclosure

- **Tools / skills:** Gemini
- **Purpose:** Mentorship on Kotlin multi-threading concepts (race conditions) and translation of my technical notes into professional B1 English.
- **Representative prompts:** "How does CountDownLatch work in Kotlin?", "Explain race conditions with WebSocket threads", "Please translate my technical notes into English for my report."
- **Affected files/sections:** `REPORT.md` (grammar and translation).
- **Validation steps:** Conducted manual black-box testing with a WebSocket client extension to gather empirical data; verified the final build using `./gradlew check`.
- **Citations:** None.
- **Human-reviewed:** I discovered the deterministic testing input ("you're right") entirely on my own through manual trial and error. I reviewed the AI's English translations to ensure they accurately reflected my independent technical decisions.