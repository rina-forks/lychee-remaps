# lychee remaps

This is a collection of [lychee](https://github.com/lycheeverse/lychee) remap expressions. 

## What are remaps?

The `--remap` option rewrites URLs into other URLs. It takes a list of space-separated pairs of strings.  
The first string is the old URL regex pattern and the second string is the new URL regex pattern.
    
<details>
  <summary>
    Click here for more details.
  </summary>

  In the following example, any match of `example.com` is replaced with `example.org`:
  
  ```bash
  lychee --remap 'example.com example.org' -- https://example.com/sitemap.xml 
  ```
  
  You can use this option multiple times to remap multiple domains and you can use
  regular expressions.
  
  Remap is a powerful feature.
  Instead of just replacing domains, you can also use regular expressions to
  replace parts of the URL.
  
  For more information about the `--remap` option,
  check out the documentation at https://lychee.cli.rs and see [#620](https://github.com/lycheeverse/lychee/pull/620), [#1129](https://github.com/lycheeverse/lychee/issues/1129), and the [example config file](https://github.com/lycheeverse/lychee/blob/4d31fb777dc6ddb0f870336c0875c218c5014624/lychee.example.toml).
</details>

## 💡 Remap: Allow index files when checking a folder

When deploying HTML files to some common platforms like Netlify or Gitlab Pages (or even on a typical Apache HTTP environment), you can rely on the platform to use an "index file", namely, for an URL like https://example.com/foo, it will serve ./public/foo/index.html if such a file exists.

From the [Apache Server documentation](https://httpd.apache.org/docs/trunk/getting-started.html#content):

> Typically, a document called index.html will be served when a directory is requested without a file name being specified. For example, if DocumentRoot is set to /var/www/html and a request is made for http://www.example.com/work/, the file /var/www/html/work/index.html will be served to the client.

Here is the remap expression:

```
lychee --remap 'example.com/((?!.html).)*$ example.com/$1/index.html' https://example.com/foo
```

This command will match URLs that do not contain a slash in the last segment of the path, implying they do not have a file extension like .html, and appends /index.html to the end of these URLs. Therefore, URLs like https://example.com/foo.html won't be affected by this command, as they already have .html at the end. But URLs like https://example.com/foo will be transformed to https://example.com/foo/index.html.

## 💡 Remap: Map from a file-system path to a deployed domain

When testing a deployed website using links from pre-deployed files or a base repository, two key mappings are required:

1. **File Extension Mapping**: Convert pre-deployed file extensions to post-deployed file extensions (e.g., .md to .html).
2. **URL Mapping**: Change the protocol and base domain components of the URL from `file://local/path` to `https://www.my-deployed-site.com` (or `http://`).

```
lychee --base https://www.my-deployed-site.com  --remap '(.*)\.md $1.html' index.md
```

This can be useful when working with github sites that are generated from markdown.

The lychee documentation itself can be tested in this manner; from the root of the repo cloned from https://github.com/lycheeverse/lycheeverse.github.io, execute:

```
lychee --base https://lychee.cli.rs  --remap '(.*)\.md $1.html' --exclude-path src/content/docs/CONTRIBUTING.md "src/content/docs/**/*.mdx" "src/content/docs/**/*.md"
```

Note that in this case we have:
1. An `.md` file we exclude because we are intentionally linking to a `.md` file.
2. Two path arguments because some repo files are `.md` and some are `.mdx`

## 💡 Remap: Stack Overflow links to API endpoints

Stack Overflow implements Cloudflare bot detection that blocks automated link checkers. Use the Stack Exchange API as a workaround:

```bash
lychee --remap 'stackoverflow.com/questions/(\d+)/.* api.stackexchange.com/2.3/questions/$1?site=stackoverflow'
```

## Contributing

Please contribute your own remap expressions here! You can just edit this file and create a pull request.



