I would like to open a discussion regarding the view-balance key introduced by CARROT.

The goal of the view-balance key is to allow a third party to be able to compute a wallet's balance without having the possibility to spend any of the funds held in this wallet. This possibility is an extension of the view key concept existing in the current Monero implementation and is a great addition for many scenarios where a wallet owner may want (or have) to make the balance and outgoing transactions viewable by others.

The view-balance secret (s_vb) coexists with an incoming view key (k_v) which allows identification of incoming enotes as in the current Monero implementation.

The current CARROT proposal defines those keys for all newly created wallets. This implies that the view-balance secret s_vb will be available for all wallets and may therefore become a piece of information highly sought after by actors wishing to peek at transactions coming in and out of wallets. Some actors, or even regulations could require that this piece of information be revealed hence jeopardizing the privacy aspects of Monero we all value.


Proponents of the view-balance secret often explain that the existing view key already allows to identify spent outputs with a "high probability" and that therefore the introduction of the view-balance secret doesn't change the expected privacy. They also argue that a user could be forced to provide key images and therefore that there is no guarantee of privacy.

The difference with the current situation is that a "high probability" is not a "100% probability" and this difference, however tiny, may be decisive in a court case, at least in many jurisdictions. As for the key images, indeed a user could be compelled to reveal them or face further charges, but the decision would be his (or hers). The introduction of the view-balance secret changes this situation completely since the revealing of it gives access to the current balance and all future balances of the wallet, whereas providing key images is only doing so if systematically done for all future outputs, which, again, would probably be something difficult to obtain in many jurisdictions.

This is a point I raised concerns about earlier this year in the Monero Policy Workgroup (https://matrix.to/#/!aFGXKcPvUExSWMkVYu:matrix.org/$plHhR1-0mDiifpGDnCNqYviVtmgFQ2R4TGShL8c2c9c). @jeffro256 replied very interestingly with the possibility to set s_vb to k_ps (the prove spend key which allows to spend the enotes of the wallet) which would make the revealing of s_vb actually equivalent to giving full access to the wallet.

This possibility should open an interesting discussion about the default setting to use for the view-balance secret within CARROT wallets.

If the view-balance secret s_vb is set to the prove spend key k_ps then requesting that the view-balance secret be provided would go against the right to property as the privilege of spending the enotes in the wallet would basically be relinquished. This could most likely be challenged in courts of many jurisdictions.

Making the default CARROT wallets use k_ps for s_vb is fully in line with the privacy ethos of Monero until now. Having a different value for s_vb could be done to create "auditable" wallets which may be required in some situations (NGOs donation addresses, wallets used to interact with CEXes, business wallets, ...). Having the ability to create "auditable" wallets at the request of the user is an interesting addition to Monero.

If "auditable" wallets were to become the default, chances are very high that wallet developers, especially commercial ones, would not go the extra mile to implement the support for non auditable wallets. This is especially true for hardware wallet providers, and chances are high that marketing/legal departments would not approve an option to provide non auditable wallets.

On the contrary, if non auditable wallets are the norm and "auditable" wallets are optional, chances are no vendor would risk being seen as supporting only "auditable" wallets as users would then vote with their feet and prefer a solution which supports both wallet types.

To preserve the strong privacy focused ethos of Monero, it is important that the choice of creating an auditable wallet be available to the largest user base possible, and not only to those able to patch existing wallet software. This would actually make those with that ability stand out and their behavior as being "criminally enclined" since they would intentionally alter software to hide something.

This is very similar to the ZEC ever continuing controversy of not offering privacy by default but making shielded tx an option, we all see how this ended, hardly anyone uses them.

So I would like to initiate a discussion and get as much feedback as possible so the community as a whole can decide if auditable wallets should be the default or not once CARROT is live.

I want to thank @jeffro256 for his tremendous work on CARROT, my proposal is in no way critical of CARROT, quite the contrary, it is just expressing concerns about the future of Monero and how choices which may seem convenient could reshape the privacy and risks of users of Monero around the world.
