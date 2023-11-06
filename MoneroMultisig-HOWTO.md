# Overview

Creating a multisig Monero wallet is a multi step process involving transmission of intermediate infos among signers. The process needs to be performed in a strict manner as failure to do so would require starting over.

# Standard wallet creation

A multisig wallet is a morphed standard wallet, so the first step is to create a standard wallet using the `monero-wallet-cli` program:

```
monero-wallet-cli --daemon-address stagenet.community.rino.io:38081 --trusted-daemon --stagenet --offline
```

Choose a name for your wallet file, set and confirm a password for it and display the seed phrase. Write down the seed phrase on a piece of paper in case the process gets interrupted before the wallet is converted to a multisig one.

Note that this standard wallet cannot make use of a hardware wallet as they do not support multisig transaction creation.

You may exit `monero-wallet-cli` after this step if you wish.

# `prepare_multisig`

The actual process of converting a standard wallet into a multisig one starts with a call to the `prepare_multisig` command. Before this command can be called, you need to enable the creation of a multisig wallet by issueing:

```
set enable-multisig-experimental 1
```

As you will need to copy some long strings during the conversion process, you may want to disable the locking of `monero-wallet-cli` as it would wipe those strings forcing you to start over the whole process since the required output would no longer be visible. Therefore it is recommended to issue:

```
set inactivity-lock-timeout 0
```

Once the conversion is finished, you can set the inactivity timeout to its default value via:

```
set inactivity-lock-timeout 90
```

The `prepare_multisig` command will output a cryptic string looking like:

```
MultisigxV2R1fCRpVinasx.....
```

Encrypt this string using the GPG keys of the other signers and send it to them via a secure channel. The other signers will do the same on their end so you will receive N-1 such messages.

You may exit `monero-wallet-cli` after this step if you need to.

# `make_multisig`

Once you have received the outputs of the `prepare_multisig` step from all the other participants, you make convert the standard wallet into a multisig one by issueing the following command:

```
make_multisig M output[1] output[2] .... output[N-1]
```

You must be ultra careful at this step, if you call `make_multisig` with less than `N-1` outputs as parameters, your wallet will be converted to a multisig wallet but not in a `M/N` setup. As this step is irreversible, you would have to start the whole process all over again, which is something the other participants may thank you for with tar and feathers.

Assuming you did not bloop, you can send the output `Multisigx...` to all the other participants in an encrypted way over a secure channel.

You may exit `monero-wallet-cli` after this step and relaunch it later.

# `exchange_multisig_keys`

The final step of the conversion process consists in calling `exchange_multisig_keys` with the outputs generated at the previous step by all the other participants.

```
exchange_multisig_keys Multisigx.... Multisigx....   ....
```

Note that if you are creating a multisig wallet with a threshold lower than the number of participants, the above command will output a `Multisigx....` string one more time. Again those strings must be transmitted in encrypted form over a secure channel among all the participants and a final call to `exchange_multisig_keys ....` must be performed.  **ATTENTION** the two calls to `exchange_multisig_keys` **MUST** be done without quiting `monero-wallet-cli` between them. Failing to do so will make the conversion process fail and will force you to start from the beginning.

Once the last `exchange_multisig_keys` call succeeds, the address of the generated multisig wallet will be displayed. Compare this address with that of the other participants. If all addresses match, congratulations you have successfully created a Monero multisig wallet!

# Backing up your multisig wallet

Once the multisig wallet has been successfully created it is important that you back up your wallet. This is done using the `encrypted_seed` command. That command will prompt you for a passphrase (seed offset passphrase) which will protect the wallet backup. It is highly recommended to use one, even though you may choose to use an empty one which will lead to a cleartext backup. Cleartext backups can be recognized easily as they start by `0200000003000000`.

Store the backup string in a very secure manner as you would store a 25 words seed.

# Restoring your multisig wallet

You may want to test your backup string by restoring the wallet in another file using the `--restore-multisig-wallet` option to `monero-wallet-cli`.

# Receiving Monero in a multisig wallet

The multisig wallet primary address or any subaddress you create may be the recipient of a Monero transaction, there is no specificity when it comes to receiving Monero in a multisig wallet, the sender has no way of knowing the wallet is a multisig by just looking at the address.

# Sending Monero using a multisig wallet

Making a transfer is a little more complex with a multisig wallet than it is with a standard wallet. The added complexity is caused by the fact that no single participant has enough information to spend any of the outputs. Before a transfer can be initiated, the missing pieces of the key images must be retrieved from `M-1` other participants. You can determine there are missing infos when the mention `Some owned outputs have partial key images - import_multisig_info needed` is present. The infos can be exported to a file using `export_multisig_info FILENAME`. This file can then be securely sent to the other participants who can import the infos using `import_multisig_info FILENAME`.

Once the key images are complete, you can issue a transfer using a standard `transfer` or `sweep_...` command. The issued command will generate a file named `multisig_monero_tx` which will require being signed by `M-1` other participants. To this end, securely transfer the file to one of the `M-1` other participants. That participant can then issue a `sign_multisig multisig_monero_tx` command which will display the details of the transaction, ask the participant to validate it and if validated will sign the transaction and will write it to a file named `multisig_monero_tx`. That file can then be passed to the next participant for signing. The last of the `M` signers will receive a message indicating that the transaction is signed and can be submitted using `submit_multisig FILENAME`. Once the `submit_multisig` call is performed, the transaction will be broadcast and will await being mined.

