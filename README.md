# torrust-installer
This is an Ansible-based installer for [Torrust](https://github.com/torrust/torrust), hacked to run on macOS for development purposes.

Tested on macOS 12.0.1, 2020 M1 MacBook Air.

## Requirements

You will need the following:

* A computer with Ansible installed (hint: brew install ansible or follow [these directions](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-specific-operating-systems)).
* A Mac you wish to develop on (hint: it's probably the same computer)
  The folder /opt/torrust created and writable on that Mac (hint: owned by your user, in other words)

## Getting started

Check out a copy of this repository.

```
git clone https://github.com/zorlin/torrust-installer
cd torrust-installer
```

Install required Ansible Collections

```
ansible-galaxy install -r collections/requirements.yml
```

Generate an SSH key if you don't already have one.

```
ssh-keygen
```

Copy your SSH key to your Torrust server (replace example.com with your server's address/domain name).

```
ssh-copy-id example.com
```

Add your Torrust server to the **inventory** file.

```
echo localhost >> inventory/hosts
```

Create a "host variables folder" where you will set keys, passwords, URLs and other settings.

```
mkdir inventory/host_vars/localhost
```

Create a variable file in that folder with all the basic configuration needed to start your tracker (paste this whole block, then open inventory/host_vars/localhost/vars.yml in an editor and change it as needed)

```
cat > inventory/host_vars/localhost/vars.yml << EOF
---
# Variables used for localhost

# Set URLs for the site and tracker
your_domain: "localhost"
# Leave this setting as-is to make your tracker use the same URL as your site.
tracker_url: "{{ your_domain }}"

# Used for internal authentication.
http_api_access_token: this-is-an-example-token-please-change-me
secret_key: this-is-an-example-secret-please-change-me

# Used to receive email alerts about expiring Let's Encrypt certificates.
certbot_email: example@protonmail.com
EOF
```

Now you're ready to rock! Run Ansible and watch as your tracker (hopefully) comes online automatically:

```
ansible-playbook site.yml
```

If the user you are connecting as requires a sudo password, run this command insatead.

```
ansible-playbook site.yml --ask-sudo-pass
```

You should see a message like this once Ansible finishes, indicating a successful run:

```
TASK [Success!] **********************************************************************************************
ok: [localhost] => {
    "msg": [
        "If you're reading this, Torrust has been successfully deployed to site localhost.",
        "Let us know if it worked, and feel free to reach out if you need help. Enjoy!"
    ]
}
```

Run the backend and tracker in a tmux session.
```
tmux new -s torrust-tracker -d 'cd /opt/torrust/torrust-tracker ; ./target/release/torrust-tracker'
tmux new -s torrust-index -d 'cd /opt/torrust/torrust/backend ; ./target/release/torrust'
```

You should now have a working tracker. Login and have a look around!

## Credits

torrust-installer was created by [Zorlin](https://github.com/zorlin/) and donated to the Torrust project in February 2022.
