# Patched Gemini CLI

This repository contains a patched version of the Google Gemini CLI that prevents automatic downgrading from gemini-2.5-pro to gemini-2.5-flash, regardless of latency issues or rate limits.

## Modifications

The following modification was made to the codebase:

- **Disabled Auto-Downgrade Logic**: Modified `packages/core/src/utils/retry.ts` to remove the code that switches from gemini-2.5-pro to gemini-2.5-flash when multiple 429 errors (Too Many Requests) are detected for OAuth users. This keeps the CLI consistently using gemini-2.5-pro even under high latency conditions.

## Benefits

- Consistently uses the more capable gemini-2.5-pro model
- Maintains all original functionality, authentication flows, and rate limit handling
- Only prevents the automatic model fallback, nothing else is affected

## Trade-offs

- May experience more 429 errors (Too Many Requests) if you hit rate limits
- Response times might be slower during high usage periods, as the pro model is more resource-intensive

## Installation

The patched Gemini CLI has been installed globally on your system:

1. The original `@google/gemini-cli` package was uninstalled
2. This patched version was built and linked globally

## How to Revert

If you wish to revert to the original Gemini CLI:

```bash
# Uninstall the patched version
npm uninstall -g @google/gemini-cli

# Install the official version
npm install -g @google/gemini-cli
```

## Technical Details

The patch specifically removes the model fallback logic in the `retryWithBackoff` function within `packages/core/src/utils/retry.ts`, which was triggered when:

1. A user is authenticated with Google OAuth (personal account)
2. Multiple 429 errors are encountered consecutively
3. The `onPersistent429` callback is available

The removed code would have downgraded from gemini-2.5-pro to gemini-2.5-flash and reset the retry counter. With the patch, the CLI will continue to respect rate limits but won't change models.
