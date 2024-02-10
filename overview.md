# Overview

Rosen bridge is an Ergo-centric bridge enabling users to send and receive coins and tokens between Ergo and any other chain. The other chain, say chainX, however, should support multi-signature transactions or threshold signatures.

In this bridge, there is no need to deploy and utilize smart contracts on the other chains since the consensus on any action can be done on the Ergo platform by a set of Guards, resulting in a signed transaction (txn for Ergo or chainX). These transactions can be broadcasted to the other chain by any party including the Guards.

Please see [this video](https://www.youtube.com/watch?v=Xsiy-yPJQ6w) for a visual introduction.
