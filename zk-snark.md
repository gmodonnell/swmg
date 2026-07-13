# Zero-Knowledge Succinct Non-Interactive Arguments of Knowledge

How can we prove we have sensitive knowledge without
revealing the sensitive knowledge itself? How can we
do it without a challenge-response interaction?
The zk-SNARK does this.

## The Pieces of zk-SNARK

**zk**: Zero-Knowledge - Proving we have info without revealing it
**S**: Succinct - proofs are small and fast to verify
**N**: Non-Interactive - proof can be verified without back and forth communication
**AR**: Argument - computationally sound
**K**: Knowledge - the underlying data we are proving we have access to

## ZKPs

zk-SNARK is an evolution of zero-knowledge proofs, which
are the ability to prove you know something without
revealing what that something is. Every ZKP must
satisfy three conditions:

* Completeness: If the proof is true, an honest prover
can always convice the verifier.
* Soundness: If the proof is false, a dishonest prover
can never convince the verifier.
* Zero-Knowledge: No information is revealed to any party
beyond the validity of the statement being proven.

##
https://www.youtube.com/watch?v=YItbsm6Ya-E
https://www.youtube.com/watch?v=6uGimDYZPMw&list=PL8Vt-7cSFnw29cLUVqAIuMlg1QJ-szV0K&index=2
https://rdi.berkeley.edu/zk-learning/
https://www.di.ens.fr/~nitulesc/files/Survey-SNARKs.pdf
