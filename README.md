
# Trufflehog3  Actions Scan :pig_nose::key:

[Trufflehog3](https://github.com/feeltheajf/Trufflehog3) is an enhanced version of [TruffleHog](https://github.com/trufflesecurity/truffleHog) scanner.

It searches through repository for secret. Trufflehog3 is an open-source tool written in Python that searches through git 
commit history for credentials/secrets that are accidentally committed. 
trufflehog3 can search for secrets in git repository as well as local directory on your machine.

This workflow is intended as a Continuous Integration secret scan in a repository. 

- Trufflehog3 scan depth can be adjusted using Custom Arguments (see below).
- Trufflehog3 scans the whole history of branches and commits, thus nothing will be missed if committed earlier.
  However we can tune to disable Git history checks - scan simple files/directories.

- Trufflehog3 allows for both regex based and high entropy based flagging.

- Trufflehog3 support custom regexes check using rules flag.
- Trufflehog3 outputs its results to the terminal. The potential secrets are coloured in bright yellow using ANSI escape codes.
We can redirect output to file in different formats: text, JSON, YAML, HTML.

## Installation

Package is available on [PyPI](https://pypi.org/project/Trufflehog3)

```
pip install Trufflehog3
```

## Way Trufflehog3 works
This module will go through the entire commit history of each branch, and check each diff from each commit, and check for secrets. This is both by regex and by entropy. For entropy checks, Trufflehog3 will evaluate the shannon entropy for both the base64 char set and hexidecimal char set for every blob of text greater than 20 characters comprised of those character sets in each diff. If at any point a high entropy string >20 characters is detected, it will print to the screen.
Entropy check can be noisy. It can be good for OSS review, bug bounties and pen test but not recommended for DevSecOps Pipeline. Hence, we are disabling at present but can be enabled at any moment as per requirement.


## Helpful Commands

```
usage: trufflehog3 [options] source

Find secrets in your codebase.

positional arguments:
  source             URLs or paths to local folders for secret searching

optional arguments:
  -h, --help         show this help message and exit
  -v, --verbose      enable verbose logging {-v, -vv, -vvv}
  -c, --config       path to config file
  -o, --output       write report to file
  -f, --format       output format {text, json, yaml, html}
  -r, --rules        ignore default regexes and source from file
  -R, --render-html  render HTML report from JSON or YAML
  --branch           name of the repository branch to be scanned
  --since-commit     scan starting from a given commit hash
  --skip-strings     skip matching strings
  --skip-paths       skip paths matching regex
  --line-numbers     include line numbers in match
  --max-depth        max commit depth for searching
  --no-regex         disable high signal regex checks
  --no-entropy       disable entropy checks
  --no-history       disable commit history check
  --no-current       disable current status check
```
## What to do in case secret is False Positive 
Trufflehog3 provide feature to ignore a false positive secret.
- option to exclude files/directories, see [trufflehog.yaml](https://github.com/feeltheajf/truffleHog3/blob/master/examples/trufflehog.yaml)

Below flags are available to ignore false positive.
- --skip-strings     skip matching strings
- --skip-paths       skip paths matching regex


```
skip_strings:
  # will be skipped in all files
  /:
    - test_key
    - test_pwd
  # will only be skipped in corresponding files
  /config/main.yml:
    - main_key
    - main_pwd

skip_paths:
  - .*key.json

```

## Custom Arguments
- **--since-commit** : Only scan from a given commit hash. We can use $(git rev-parse HEAD) to find current commit id and look only in current changed files.
- **--max-depth** : The max commit depth to go back when searching for secrets. By default, it's 50, but we can customize as per need.
- **--no-history** : This flag is used to disable Git history checks - scan simple files/directories
- **--no-entropy** : This flag is used to disable entropy check. If at any point a high entropy string >20 characters is detected, it will print to the screen and fail pipeline. By default, we are keeping it False to reduce noise.
- **--line-numbers** : This flag will print line number as well in trufflehog3 secrets output.
- **-o, --output** :  This flag will write scan report to file.
- **-f, --format** : This flag is used to get output file in different formats: text, JSON, YAML, HTML

## Customized Regex rules:

Custom regexes can be added with the following flag --rules /path/to/rules.json. This should be a json file of the following format:
**usage:**  --rules /path/to/rules.json<br />

```
{ "RSA private key": "---EC PRIVATE KEY---"}
```

[List of Trufflehog3 default regexes check](https://github.com/feeltheajf/trufflehog3/blob/2.x/truffleHog3/rules.yaml)


**Hope this could help you find your exposed secret in Git repositories before the hackers do 🤞**

----
## Join The Slack
Have questions? Suggestions? Jump in slack and hang out with us.
#### Channel #Trufflehog
https://join.slack.com/share/zt-qeav9eed-FavYtqvepKsrt0fh_hE0Xw
