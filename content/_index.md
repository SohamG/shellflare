+++
title = "Shellflare - upload to Cloudflare with sh"
template = "index.html"
+++

# Introduction

***Shellflare*** is a single file POSIX Shell script with minimal dependencies that
uploads static assets to Cloudflare workers. Use shellflare to instantly deploy
the output of a static site generator, documentation generator or any other file,
and have it served as static content via HTTP on Cloudflare.

# Usage

## Dependencies

Apart from a POSIX compliant shell (bash, zsh, etc), the following are needed:

 - The command `sha256sum` (preferred) or `openssl` (useful for non GNU-based setups)
 - The [jq](https://jqlang.org) JSON CLI tool
   + Perhaps will support `yq` in the future.
 - Curl
 - Standard POSIX utilities like `find`, `cat` etc.
 - A cloudflare account and API token.

## Installation

```sh
$ curl https://shellflare.sohamg.xyz/sh > /path/to/where/you/want/it.sh
```

That's it! Its a shell script, so make sure to mark executable. Done.

## Invocation

 - Set environment variable `CFLARE_API_KEY` to your API token.
   + I could add support for reading this from a file/command if desired.

 - Obtain usage information and settings:

   ```sh
   $ ./shellflare -h
   ```
 - Common invocation to deploy contents of `./public` to cloudflare:

   ```sh
   $ ./shellflare -e user@domain.tld -n "worker-name"
   ```

 - Header and Redirect configuration is taken from the files `_headers` and
   `_redirects` from the current directory. Contents of the files are the same
   as in the Cloudflare documentation.
   
# Source Code

[Shellflare on Github](https://github.com/SohamG/shellflare/blob/5eca8c7718db6f4b9efe92fd1d69925f6b8550c0/shellflare)

# Security Considerations

All input to the script including via Environment Variables and CLI flags is
assumed to be trusted/non malicious. If you have something bad in those, the
script will probably do said bad thing. This is not a bug given the use
case. The script is run as the user of the invoking shell, which MUST have
appropriate permissions. User is responsible for the security of the API key.

# License

[Mozilla Public License 2.0](https://www.mozilla.org/en-US/MPL/)

Content uploaded using this script naturally belongs to the user or its
respective owner.

# Motivation

I wanted to use/try [Cloudflare Pages](https://pages.dev), but turns out it's
on the chopping block. So I looked at [Workers](https://workers.dev)&nbsp;Static
Assets but all the guides from Cloudflare involve using JavaScript frameworks,
`npm` or `yarn`, and some complicated stuff about adaptors and `vite` and what
not. I am .. not a fan of JS, and I'd rather avoid it as much as I can. Even by
itself, an empty project using Cloudflare's
[Wrangler](https://github.com/cloudflare/workers-sdk) CLI tool has over
**150&nbsp;MB** of `node_modules`!

I also refuse to entertain the idea that the (seemingly) trivial task of "files
on my computer become files on Cloudflare's computer" *requires* an unwieldy
mess of TypeScript compiled to JS and pulling in a whole bunch of packages.

So I read the cloudflare API documentation, quickly realised that it blatantly
wrong, then employed various debugging and figure-outing techniques
(like changing random stuff and hope it works) and somehow managed to reproduce
`wrangler deploy` as a POSIX `sh` script.  
(Blog post about the API coming soon)

---

