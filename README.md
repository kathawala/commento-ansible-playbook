<!-- Add banner here -->
![Banner](https://i.imgur.com/bnEShwI.png)

![Github last commit](https://img.shields.io/github/last-commit/kathawala/commento-ansible-playbook)
![Github repo size](https://img.shields.io/github/repo-size/kathawala/commento-ansible-playbook)
![License](https://img.shields.io/github/license/kathawala/commento-ansible-playbook)

# Commento Ansible Playbook

I wrote about this on my blog, you can read the article [here](https://www.farhansayshi.com/post/free-one-command-self-hosted-commento-deployment-for-a-hugo-site/).

# Demo

<!-- Add a demo for your project -->

![Demo Video](https://www.farhansayshi.com/media/demonstration.gif)

# Table of contents

- [Commento Ansible Playbook](#commento-ansible-playbook)
- [Demo](#demo)
- [Table of contents](#table-of-contents)
- [Introduction](#introduction)
- [Installation](#installation)
- [Usage](#usage)
  - [Prerequisites](#prerequisites)
  - [Main](#main)
  - [Optional](#optional)
- [Contribute](#contribute)
    - [Sponsor](#sponsor)
    - [Adding new features or fixing bugs](#adding-new-features-or-fixing-bugs)
- [License](#license)
- [Thanks!](#thanks)

# Introduction

[Commento.io](https://commento.io) is an _open-source_ solution to add **comment section functionality** into any website (_even static ones_) by simply adding some javascript to the frontend and having a separate backend to store comments.

Commento has a paid, hosted solution, but you can self-host commento from a cheap VPS or from a laptop/computer at home _FOR FREE_.

**SKIP** the hassle of finding a domain name.

**SKIP** changing DNS records.

**SKIP** procuring SSL certs.

**SKIP** keeping said certs auto-renewing.

**SKIP** installing and configuring all sorts of packages and databases, etc.

Pay **NOTHING** for the convenience!

# Installation
[(Back to top)](#table-of-contents)

<!--
The first one should be how to install(how to generally use your project or set-up for editing in their machine).

This should give the users a concrete idea with instructions on how they can use your project repo with all the steps.

Following this steps, **they should be able to run this in their device.**

A method I use is after completing the README, I go through the instructions from scratch and check if it is working. -->

<!-- Here is a sample instruction:

To use this project, first clone the repo on your device using the command below:

```git init```

```git clone https://github.com/navendu-pottekkat/nsfw-filter.git``` -->

1. Make sure you have `ansible` installed on your system. [Installation instructions](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) for all systems.
2. `git clone https://github.com/kathawala/commento-ansible-playbook.git`

# Usage
[(Back to top)](#table-of-contents)

## Prerequisites

This playbook sets up the following:

1. SystemD service running commento
2. SystemD service running a postgresql database connected to commento
3. SystemD service running Nginx to route traffic to commento
4. SystemD timer (i.e. cron job) to provide SSL certs and renew them every month.

This playbook works on and has been tested for machines that run either Ubuntu 20.04 LTS or Debian 10.
I haven't tried any other operating systems, open an issue if you'd like support for another one.

To use it you **_need_** to sign in to [DuckDNS](https://www.duckdns.org/) and have a DuckDNS API token and subdomain.
This playbook uses DuckDNS because it's easy.
If another way is best for you, open an issue or submit a PR. We'll try to make it work.

Also make sure you have set up ansible to point at the machine you want to host commento on. More info on [how to do that, here](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html). 

## Main

1. Enter the root directory of the repo.
   ```bash
   cd commento-ansible-playbook
   ```
2. Add your subdomain from DuckDNS to the file `vars/vars.yaml`.
   1. If your domain name is example.duckdns.org then `vars/vars.yaml` should look like:
      ```yaml
      ---
      VERSION: 1.8.0
      DOMAIN_NAME: "example.duckdns.org"
      SITE_URL: "example.duckdns.org"
      ```
   2. If you want to run commento on a non-standard port (e.g. 4430), change `SITE_URL` as follows:
      ```yaml
      ---
      VERSION: 1.8.0
      DOMAIN_NAME: "example.duckdns.org"
      SITE_URL: "example.duckdns.org:4430"
      ```
      You also need to take the extra step of adding your nonstandard port to the nginx configuration template `commento-ssl.conf.j2`:
      ```
      listen                  [::]:4430 ssl ipv6only=on;
      listen                  4430 ssl;
      ```
3. Enter your GMail address information (for SMTP relay), [DuckDNS](https://www.duckdns.org/) token (for DNS), [Akismet](https://akismet.com/) key^ (for automated comment moderation) and a database password of your choice to `vars/secure_vars.yaml`. Here's an example:
   ```yaml
   ---
   DB_PASSWORD: strongandsecurepassword
   GMAIL_ADDRESS: commentguy@gmail.com
   GMAIL_PASSWORD: 'pwdwithspecialchar"#&'
   AKISMET_KEY: 12345hsjd
   DUCKDNS_TOKEN: 123abc-09zy-12ab-91za-blahblahblah
   ```
^ optional

4. Allow ["Less Secure App Access"](https://myaccount.google.com/lesssecureapps) in GMail. Your SMTP relay will not work otherwise, and you need that to allow commenters to sign up with their own email address.

5. Run the `full_setup.yaml` playbook like follows
   ```bash
   ansible-playbook -i hosts full_setup.yaml --ask-become-pass
   ```
   _**note:**_ If your host machine runs Debian 10, ansible won't automatically determine the correct python interpreter, so you need to add the following flag `-e 'ansible_python_interpreter=/usr/bin/python3'` to the above command
6. Done! Visit https://example.duckdns.org (replace with the domain name you entered in step 2) and register your site with your new self-hosted commento instance!

## Optional

You can use [Akismet](https://akismet.com/) for automated spam filtering in your comment sections.
It's pay-what-you-want for the lowest tier, but consider paying what you can, if you can.
If you don't want to use it, you can leave an empty string for the `AKISMET_KEY` var in `var/secure_vars.yaml`.

You can also host commento on an old computer at your house.
Just port forward the 443 and 80 ports from your router to the machine on your home network that you want to host commento.
Here's a [great guide on port forwarding](https://openmyip.com/) that covers lots of popular routers and common missteps people make while port forwarding.

If you plan to do this, you should use the [DuckDNS Dynamic DNS tool](https://www.duckdns.org/install.jsp) to update DuckDNS with your home's IP address (as ISPs often rotate out the IP address assigned to your home).

# Contribute
[(Back to top)](#table-of-contents)

### Sponsor

Free comment section functionality on your blog in 15 minutes or less!

That's a good deal! Consider donating to the services that make this possible:

* DuckDNS -- [PayPal](https://www.paypal.com/donate/?token=H64IP14XRMgT37GwnVZGdyf2M1cXcJQZyQq76ujn0zLAJFIBHvRWownwLStmImk0RKQhem&country.x=GB&locale.x=GB) | [Patreon](https://www.patreon.com/user?u=3209735) | btc:16gHnv3NTjpF5ZavMi9QYBFxUkNchdicUS
* Commento -- consider [paying for a month or two](https://commento.io/pricing) of commento.io

### Adding new features or fixing bugs

Open an issue or a PR if there is a configuration you'd like to see. Maybe an OS that isn't supported or something else I hadn't thought of.

# License
[(Back to top)](#table-of-contents)

[GNU General Public License version 3](https://opensource.org/licenses/GPL-3.0)

# Thanks!
[(Back to top)](#table-of-contents)

Leave a star on GitHub, check out [my blog](https://www.farhansayshi.com), and share this playbook if you found this helpful.

<!-- Add the footer here -->

![Footer](https://i.imgur.com/tj5eABk.png)
