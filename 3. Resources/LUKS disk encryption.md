# Summary: [[TPM2]] Auto-Unlock for LUKS ([[Fedora]])

This process binds your disk encryption key to your computer's [[TPM2]] chip, allowing for automatic decryption at boot while keeping your original passphrase as a secure fallback.

## Prerequisites

*   Ensure **Secure Boot** is enabled in your [[BIOS]]/[[UEFI]].
*   Identify your LUKS partition (e.g., `/dev/nvme0n1p3`) using `lsblk`.

## Implementation

1.  **Enroll [[TPM2]]:** Add the TPM key to the LUKS partition.
    `sudo systemd-cryptenroll --tpm2-device=auto --tpm2-pcrs=7 /dev/nvme0n1p3`
2.  **Update Config:** Edit `/etc/crypttab` and add `tpm2-device=auto` to your partition entry.
3.  **Update [[Initramfs]]:** Rebuild the initramfs to apply changes.
    `sudo dracut -fv --regenerate-all`
4.  **Reboot:** Your system will now attempt to use the TPM to unlock the disk automatically.

## Cleanup (Custom [[Polkit]] Removal)

To remove your previous [[Polkit]] configuration:
`sudo rm /etc/polkit-1/rules.d/90-udisks2.rules && sudo systemctl restart polkit`