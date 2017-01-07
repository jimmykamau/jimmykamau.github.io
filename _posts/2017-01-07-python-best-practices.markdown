---
layout: post
title: "Python best practices"
date: 2017-01-07
categories:
  - Python
  - Code
description: "Reformatting a project to comply with Python's best practices"
image: /assets/images/2017-01-07/best_practices.jpg
image-sm: /assets/images/2017-01-07/best_practices.jpg
---

Conforming to a language's best practices doesn't only help us to write maintainable code, but also helps in the process of debugging. Python has its own rules and regulations that are considered best practices.

We will go through a number of these best practices, namely:
* Multiple statements on one line

To demonstrate this, we will contribute to an open-source project, [AutoNessus](https://github.com/redteamsecurity/AutoNessus), changing its code to conform to some best practices.

Some background on my development environment: I use [Sublime Text](https://www.sublimetext.com/) for my text editor. You can configure it for Python development by following [this](https://realpython.com/blog/python/setting-up-sublime-text-3-for-full-stack-python-development/) tutorial.

Opening the AutoNessus code on my editor immediately throws up a number of errors. These are highlighted by the small dots at the beginning of the respective lines:
![Python errors](/assets/images/2017-01-07/python_errors.png)

## Multiple statements on one line

![Multiple statements on one line](/assets/images/2017-01-07/multiple_statements.png)
Having multiple statements on one line raises PEP8's error 701. We can correct this by having the statements on separate lines:

```Python
try:
    import requests
except:
    print('Need to install the Requests module before execution')
    exit()
...


try:
    token = login(username, password)
except:
    print('Unable to login :(')
    exit()
```

Also note that we no longer need the semicolon after the `print` statement.

## Line too long

![Line too long](/assets/images/2017-01-07/line_too_long.png)
PEP8 recommends having lines that are 79 characters at most. This is a challenge that I've observed with quite a number of programmers. Having short lines makes your code readable without having to scroll horizontally.
In our example above, we can shorten the lines by separating the statements inside parentheses:

```Python
group.add_argument(
    '-l', '--list', dest='scan_list',
    action='store_true', help='List current scans and IDs')
group.add_argument(
    '-p', '--policies', dest='policy_list',
    action='store_true', help='List current policies')
group.add_argument(
    '-sS', '--start', dest='start_scan_id',
    type=str, help='Start a specified scan using scan id')
group.add_argument(
    '-pS', '--pause', dest='pause_scan_id',
    type=str, help='Pause a specified scan using scan id')
group.add_argument(
    '-sP', '--stop', dest='stop_scan_id',
    type=str, help='Stop a specified scan using scan id')
...


print(
  '%s' + (20 - option_length_menu) * ' ' + '  :    %s' +
  (15 - option_length_text) * ' ' + ':    %s') % (
  menu, text, other)
...


if method == 'POST':
    r = requests.post(
        build_url(resource), data=data, headers=headers, verify=verify)
elif method == 'PUT':
    r = requests.put(
        build_url(resource), data=data, headers=headers, verify=verify)
elif method == 'DELETE':
    r = requests.delete(
        build_url(resource), data=data, headers=headers, verify=verify)
else:
    r = requests.get(
        build_url(resource), params=params, headers=headers, verify=verify)
...


print(
  'If you need to start a previously completed scan,'
  'add "completed" to the list on line 269')
```

## Expected 2 blank lines, found 1

![2 blank lines](/assets/images/2017-01-07/blank_lines.png)
PEP8 recommends having at least two blank lines before class and function (except method) declarations and after module imports

```Python
password = 'xxxxx'


class create_menu:
...


return


def build_url(resource):
    return '{0}{1}'.format(url, resource)


def connect(method, resource, data=None, params=None):
...


return data['scan_uuid']


def pause(sid):
    # Pause the scan specified by the sid.
    connect('POST', '/scans/{0}/pause'.format(sid))
    return


def resume(sid):
    # Resume the scan specified by the sid.
    connect('POST', '/scans/{0}/resume'.format(sid))
    return


def stop(sid):
    # Resume the scan specified by the sid.
    connect('POST', '/scans/{0}/stop'.format(sid))
    return


def logout():
```

The reverse is also true, having more that two blank lines raises PEP8 error 303
![Too many blank lines](/assets/images/2017-01-07/too_many_blank_lines.png)

## White spaces around operators

PEP8 has a number of recommendations regarding white spaces around operators, as detailed by the E2** error codes

```Python
print('%s' + (
  15 - option_length_menu) * ' ' + '  :  %s') % (menu, text)
...


temp_hist_dict_rev = {a: b for b, a in temp_hist_dict.items()}
try:
    for key, value in temp_hist_dict_rev.items():
        print key
...


for title, uuid in policy_dict.items():
    create_menu(title, uuid, 'Null')
```

## Comments

One-line Python comments begin with a `#`. Adding multiple hash characters after the first one like has been done below isn't necessary
![Comments](/assets/images/2017-01-07/comments.png)

```Python
print('Logged in!\n\n')

# Display all policies
if args.policy_list:
    # If -p flag is specified, print the policy list and exit

    print "Printing Policies \n\n"
    policy_dict = get_policies()
```
