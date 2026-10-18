# ansible-playbook-onboarding

Playbook that:

1. Creates `/tmp/ansible-text-file`
2. Writes my name into it
3. Prints its contents to stdout
4. Installs `python3-devel` (`python3-dev` on Debian/Ubuntu)
5. Deletes the file

Only native modules are used (`copy`, `slurp`, `debug`, `package`, `file`).
`ansible.builtin.package` picks the right package manager (dnf/yum/apt) from
the gathered facts, and the package name is chosen from the host's OS family,
so the target OS doesn't need to be specified.

## Targets

Hosts are listed under the `dana_dev` group in [inventory/hosts.yml](inventory/hosts.yml).
Currently only `dana-dev-5`; add more hosts to the list to run on them too.

## Authentication

Ansible logs in to each target over SSH, as the user running the playbook
(set `ansible_user` in the inventory to use a different one). The targets
see it as a normal SSH login from that user, using your SSH key or password
(`-k` prompts for the SSH password). The package install runs through
`sudo` (`become`), so that user needs sudo rights. `-K` prompts for the
sudo password.

## Run

On the control VM:

```bash
sudo dnf install -y ansible-core      # or: pip install --user ansible-core
git clone git@github.com:itayShv/ansible-playbook-onboarding.git
cd ansible-playbook-onboarding

ssh-copy-id dana-dev-5                             # once, if not using a password
ansible dana_dev -m ansible.builtin.ping           # check connectivity
ansible-playbook playbook.yml --syntax-check
ansible-playbook playbook.yml --check              # dry run
ansible-playbook playbook.yml -K                   # -K prompts for the sudo password
```
