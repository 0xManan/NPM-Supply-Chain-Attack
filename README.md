# NPM Supply Chain Attack (September 8, 2025) - Analysis and Resources

## Overview

On September 8, 2025, a major supply chain attack targeted popular NPM packages, including `debug` (357M weekly downloads) and `chalk` (299M), along with dependencies like `ansi-styles`, `supports-color`, `strip-ansi`, and others. The attack involved a hijacked maintainer account (@qix on GitHub) compromised via an email-based 2FA bypass. Malicious versions were published between approximately 9:00 AM and 11:30 AM ET and have since been removed from NPM. However, the maintainer has not fully regained control, so ongoing vigilance is essential.

This attack injects malware into JavaScript-based web applications (e.g., dApps, DEX frontends), monitoring wallet interactions across chains like Ethereum (EVM), Bitcoin, Solana, Tron, Litecoin, and Bitcoin Cash. It swaps recipient addresses during transactions using Levenshtein distance to match from a hardcoded list of attacker addresses. No funds have been stolen as of September 9, 2025, likely due to the narrow exposure window.

The vulnerability affects only fresh installs or `package-lock.json` updates during the ~2.5-hour window if vulnerable packages are in direct or transitive dependencies. It primarily targets web-accessed apps; internal wallet sends (e.g., direct MetaMask transfers) are safe. This incident is similar to past attacks like the Ledger Connect Kit breach but with a broader scope.

This repository provides key resources extracted from threat research (e.g., from @officer_cia on X) to help developers and crypto users mitigate risks. It includes attacker addresses, recommendations, and a quick NPM check script.

## Key Attack Details

- **Compromised Packages and Versions**: Includes `debug@4.4.2`, `chalk@5.6.1`, `ansi-styles@6.2.2`, `supports-color@10.2.1`, `strip-ansi@7.1.1`, `ansi-regex@6.2.1`, `wrap-ansi@9.0.1`, `color-convert@3.1.1`, `color-name@2.0.1`, `is-arrayish@0.3.3`, `slice-ansi@7.1.1`, `color@5.0.1`, `color-string@2.1.1`, `simple-swizzle@0.2.3`, `supports-hyperlinks@4.1.1`, `has-ansi@6.0.1`, `chalk-template@1.1.1`, `backslash@0.2.1`.
- **Malware Behavior**: Hides in dependencies like `error-ex`; injects into web apps to hijack transactions. No automatic drainage—users must still sign, but swapped addresses could be missed without review.
- **Impact**: Affects browsers/dApp frontends; extensions and native apps are less likely impacted unless pulling compromised deps.
- **No Exploits Reported**: Limited window minimized damage, but treat potentially affected machines as compromised.

## Repository Files

- **[attacker_addresses.txt](attacker_addresses.txt)**: Full list of attacker addresses (EVM-focused but multi-chain capable). Monitor these on-chain for any activity. Example addresses include `0xFc4a4858bafef54D1b1d7697bfb5c52F4c166976`, `0xa29eeFb3f21Dc8FA8bce065Db4f4354AA683c024`, and more (58 total).

- **[Recommendations.md](Recommendations.md)**: Combined recommendations for developers and crypto users. Includes steps like auditing `package-lock.json`, running security scans, using hardware wallets, and transaction simulation tools (e.g., @blockaid_, @web3_antivirus).

- **[npm_check.sh](npm_check.sh)**: A bash script for a quick NPM dependency check. Runs `npm ls` on the affected packages piped through `grep` to flag malicious versions. Usage: `./npm_check.sh` in your project directory. Contents:
  ```
  npm ls ansi-styles debug chalk supports-color strip-ansi ansi-regex wrap-ansi color-convert color-name is-arrayish slice-ansi color color-string simple-swizzle supports-hyperlinks has-ansi chalk-template backslash | grep -P '(ansi-styles@6.2.2|debug@4.4.2|chalk@5.6.1|supports-color@10.2.1|strip-ansi@7.1.1|ansi-regex@6.2.1|wrap-ansi@9.0.1|color-convert@3.1.1|color-name@2.0.1|is-arrayish@0.3.3|slice-ansi@7.1.1|color@5.0.1|color-string@2.1.1|simple-swizzle@0.2.3|supports-hyperlinks@4.1.1|has-ansi@6.0.1|chalk-template@1.1.1|backslash@0.2.1)'
  ```

## Additional Resources

- **Scanning Tools**:
  - [GitHub Script by AndrewMohawk](https://github.com/AndrewMohawk/RandomScripts/blob/main/scan_for_deps_qix-2025-08-09.sh)
  - [Gist by edgarpavlovsky](https://gist.github.com/edgarpavlovsky/695b896445c19b6f66f141696f596059)
  - One-liner for obfuscated code: `grep -R --binary-files=without-match --color=always --exclude-dir=.git _0x112fa8 .`

- **Technical Breakdowns**:
  - [Deobfuscated Payload (Aikido.dev)](https://www.aikido.dev/blog/npm-debug-and-chalk-packages-compromised)
  - [Maintainer Update (GitHub Issue)](https://github.com/debug-js/debug/issues/1005#issuecomment-3266868187)
  - [Early Analysis (jdstaerk.substack)](https://jdstaerk.substack.com/p/we-just-found-malicious-code-in-the)

- **General Advice**: Implement dependency monitoring (e.g., via tools like @guardrailai). If compromised, reset your OS. For crypto ops, avoid transactions for a few days and double-check everything.

This repo highlights the fragility of open-source supply chains. Contributions welcome—fork and PR with updates or additional tools.
