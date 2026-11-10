# Trunk Workspaces for Orca

Run [Orca](https://github.com/stablyai/orca) workspaces in [Trunk Workspaces](https://docs.trunk.io)
cloud sandboxes. Each Orca workspace is a Trunk workspace for the repository. The template, size and
region come from the repository's `/blaze` mapping, else from the organization's defaults. Orca connects
over SSH with a 12-hour certificate, so no key goes into the sandbox.

## Set up once

You need Node.js 22 or newer, OpenSSH and OpenSSL, on macOS or Linux.

1. Download `trunk-orca.mjs` from the latest release. Compare its SHA-256 with the one in the release
   notes (`shasum -a 256 trunk-orca.mjs`).
2. Run:

   ```sh
   node trunk-orca.mjs setup
   ```

   `setup` signs you in with a device code and asks for the organization when you have more than one.
   Then it installs the adapter to `~/.config/trunk/orca/bin/trunk-orca`, where the recipe runs it.

3. In Orca, open Settings → Experimental and turn on Cloud VM. Then open Settings → Plugins and install
   this repository. Pick the **Trunk Workspaces** recipe when you create a workspace.

## What each step does

| Orca      | Trunk Workspaces                                                       |
| --------- | ---------------------------------------------------------------------- |
| `create`  | Create a workspace, wait until it answers SSH, get an SSH certificate. |
| `suspend` | Put the workspace to sleep.                                            |
| `resume`  | Wake it, and get a new certificate.                                    |
| `destroy` | Delete the workspace.                                                  |

The SSH key is `~/.config/trunk/ssh/id_ed25519`. The trunk app uses the same key.

## Files on your computer

- `~/.config/trunk/orca/session.json` (mode `0600`): your sign-in session. Delete it to sign out.
- `~/.config/trunk/orca/bin/`: the installed adapter.
- `~/.config/trunk/ssh/`: the SSH key and its current certificate.

## Troubleshooting

- **"SSH certificates are not available in this trunk environment yet."** Your environment has not
  turned on SSH certificates. Orca has no other key, so the recipe stops.
- **"Not signed in" or "The trunk session expired."** Run `setup` again.
- **`503 repositoryIdUnavailable`.** Trunk could not ask GitHub about the repository. Try again.
- **A create that failed.** The recipe deletes the workspace it made. If one stays, delete it in the
  trunk app.

## Security

Report a security problem privately: open this repository's **Security** tab and select **Report a
vulnerability**. Do not open a public issue for it.
