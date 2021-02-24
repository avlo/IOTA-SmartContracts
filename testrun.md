```bash
(develop)$ pwd
/home/nick/git/wasp/contracts/rust/donatewithfeedback/src

(develop)$ date
Wed Feb 24 14:50:41 EST 2021

(develop)$ wasm-pack build
[INFO]: Checking for the Wasm target...
[INFO]: Compiling to Wasm...
    Finished release [optimized] target(s) in 0.03s
[INFO]: Installing wasm-bindgen...
[INFO]: Optimizing wasm binaries with `wasm-opt`...
[INFO]: :-) Done in 1.05s
[INFO]: :-) Your wasm pkg is ready to publish at /home/nick/git/wasp/contracts/rust/donatewithfeedback/pkg.

(develop)$ ls -l ../pkg/
total 88
-rw-r--r-- 1 nick nick    55 Feb 24 14:50 donatewithfeedback_bg.js
-rw-r--r-- 1 nick nick 51322 Feb 24 14:50 donatewithfeedback_bg.wasm
-rw-r--r-- 1 nick nick   169 Feb 24 14:50 donatewithfeedback_bg.wasm.d.ts
-rw-r--r-- 1 nick nick    42 Feb 24 14:50 donatewithfeedback.d.ts
-rw-r--r-- 1 nick nick    97 Feb 24 14:50 donatewithfeedback.js
-rw-r--r-- 1 nick nick 12099 Feb 24 14:50 LICENSE
-rw-r--r-- 1 nick nick   504 Feb 24 14:50 package.json
-rw-r--r-- 1 nick nick   220 Feb 24 14:50 README.md

(develop)$ cd ../test/
(develop)$ ls -l
total 4
-rw-r--r-- 1 nick nick 3102 Feb 24 14:39 donatewithfeedback_test.go

(develop)$ cp ../pkg/donatewithfeedback_bg.wasm .
(develop)$ ls -l
total 56
-rw-r--r-- 1 nick nick 51322 Feb 24 14:51 donatewithfeedback_bg.wasm
-rw-r--r-- 1 nick nick  3102 Feb 24 14:39 donatewithfeedback_test.go

(develop)$ cat donatewithfeedback_test.go 
package test

import (
	"testing"

	"github.com/iotaledger/goshimmer/dapps/valuetransfers/packages/balance"
	"github.com/iotaledger/wasp/contracts/rust/donatewithfeedback"
	"github.com/iotaledger/wasp/contracts/testenv"
	"github.com/stretchr/testify/require"
)

func setupDwfTest(t *testing.T) *testenv.TestEnv {
	te := testenv.NewTestEnv(t, donatewithfeedback.ScName)
	return te
}

// DEBUG ************* PRINT STATEMENTS TO CONFIRM EXECUTION
func TestDwfDeploy(t *testing.T) {
	te := setupDwfTest(t)
	ret := te.CallView(donatewithfeedback.ViewDonations)
	results := te.Results(ret)
	t.Logf("***************\n")
	t.Logf("***************\n")
	t.Logf("TestDwfDeploy\n")
	t.Logf("***************\n")
	t.Logf("***************\n")
	max := results.GetInt(donatewithfeedback.VarMaxDonation)
	require.EqualValues(t, 0, max.Value())
	tot := results.GetInt(donatewithfeedback.VarTotalDonation)
	require.EqualValues(t, 0, tot.Value())
}
...
...

(develop)$ cat ../src/donatewithfeedback.rs 
// Copyright 2020 IOTA Stiftung
// SPDX-License-Identifier: Apache-2.0

use wasmlib::*;

use crate::*;
use crate::types::*;

// DEBUG ################## PRINT STATEMENTS TO CONFIRM EXECUTION
pub fn view_donations(ctx: &ScViewContext, _params: &ViewDonationsParams) {
    println!("##################\n");
    println!("##################\n");
    println!("view_donations\n");
    println!("##################\n");
    println!("##################\n");
    ...
}

(develop)$ go test -v -run TestDwfDeploy
--------------- reserved hnames ------------------
      cebf5908: 'root'
      3c4b5e02: 'accounts'
      fd91bc63: 'blob'
      661aa7d8: 'eventlog'
      1f44d644: 'init'
--------------- reserved hnames ------------------
=== RUN   TestDwfDeploy
55:19.995   INFO    TestDwfDeploy   solo/solo.go:160    deploying new chain 'chain1'
55:19.997   INFO    TestDwfDeploy.chain1    vmcontext/runreq.go:179 eventlog -> '[req] [0]Dvda2WJUdyFGY8caG1vhW6rZcjpCBSATkZ5NcnS9mSpQ: Ok'
55:19.998   INFO    TestDwfDeploy.chain1    solo/run.go:82  state transition #0 --> #1. Requests in the block: 1. Posted: 0
55:19.998   INFO    TestDwfDeploy   solo/clock.go:44    ClockStep: logical clock advanced by 1ms
55:19.998   INFO    TestDwfDeploy.chain1    solo/solo.go:240    chain 'chain1' deployed. Chain ID: U6NcU364Zwzfb15yDfmR73e4Gmp63CaVcsM5gfFNVPUw
55:19.998   INFO    TestDwfDeploy.chain1    solo/req.go:172 callView: blob::getBlobInfo
55:19.999   INFO    TestDwfDeploy.registry.registry registry/blobcache.go:43    data blob has been stored. size: 51322 bytes, hash: HX8TMECDB8r8qUwf2BiBGwT2BohvwGzXwfXHJw2juPsn
55:19.999   INFO    TestDwfDeploy   solo/solo.go:382    Solo::PutBlobDataIntoRegistry: len = 51322, hash = HX8TMECDB8r8qUwf2BiBGwT2BohvwGzXwfXHJw2juPsn
55:19.999   INFO    TestDwfDeploy.chain1    solo/req.go:172 callView: root::getFeeInfo
55:20.000   INFO    TestDwfDeploy.chain1    solo/req.go:147 PostRequest: blob::storeBlob -- [0]CSoRHx9Tjy1bez1TNTgaCYTMGcTeECrbyvXqDUB4oCtq
55:20.001   INFO    TestDwfDeploy.chain1    vmcontext/log.go:4  eventlog::fd91bc63 -> '[blob] hash: 5Edi3aG2oGsFjr4JjdzgsCRVHstnjkCWE8vcNAtG5HCy, field sizes: [51322 10]'
55:20.001   INFO    TestDwfDeploy.chain1    vm/event.go:24  U6NcU364Zwzfb15yDfmR73e4Gmp63CaVcsM5gfFNVPUw::fd91bc63/event [blob] hash: 5Edi3aG2oGsFjr4JjdzgsCRVHstnjkCWE8vcNAtG5HCy, field sizes: [51322 10]
55:20.001   INFO    TestDwfDeploy.chain1    vmcontext/runreq.go:179 eventlog -> '[req] [0]CSoRHx9Tjy1bez1TNTgaCYTMGcTeECrbyvXqDUB4oCtq: Ok'
55:20.003   INFO    TestDwfDeploy.chain1    solo/run.go:82  state transition #1 --> #2. Requests in the block: 1. Posted: 0
55:20.003   INFO    TestDwfDeploy   solo/clock.go:44    ClockStep: logical clock advanced by 1ms
55:20.003   INFO    TestDwfDeploy.chain1    solo/req.go:147 PostRequest: root::deployContract -- [0]ARKL5F3huM9KfC5LVCZ98wwy49X6u8uZzQ6n3HzN7WEw
55:20.102   INFO    TestDwfDeploy.chain1    vmcontext/log.go:4  eventlog::cebf5908 -> '[deploy] name: donatewithfeedback hname: 696d7f66, progHash: 5Edi3aG2oGsFjr4JjdzgsCRVHstnjkCWE8vcNAtG5HCy, dscr: 'N/A''
55:20.102   INFO    TestDwfDeploy.chain1    vm/event.go:24  U6NcU364Zwzfb15yDfmR73e4Gmp63CaVcsM5gfFNVPUw::cebf5908/event [deploy] name: donatewithfeedback hname: 696d7f66, progHash: 5Edi3aG2oGsFjr4JjdzgsCRVHstnjkCWE8vcNAtG5HCy, dscr: 'N/A'
55:20.102   INFO    TestDwfDeploy.chain1    vmcontext/runreq.go:179 eventlog -> '[req] [0]ARKL5F3huM9KfC5LVCZ98wwy49X6u8uZzQ6n3HzN7WEw: Ok'
55:20.102   INFO    TestDwfDeploy.chain1    solo/run.go:82  state transition #2 --> #3. Requests in the block: 1. Posted: 0
55:20.102   INFO    TestDwfDeploy   solo/clock.go:44    ClockStep: logical clock advanced by 1ms
55:20.102   INFO    TestDwfDeploy.chain1    solo/req.go:172 callView: donatewithfeedback::donations
55:20.105   INFO    TestDwfDeploy   testenv/testenv.go:150  Direct access to results
    donatewithfeedback_test.go:21: ***************
    donatewithfeedback_test.go:22: ***************
    donatewithfeedback_test.go:23: TestDwfDeploy
    donatewithfeedback_test.go:24: ***************
    donatewithfeedback_test.go:25: ***************
--- PASS: TestDwfDeploy (0.11s)
PASS
ok      github.com/iotaledger/wasp/contracts/rust/donatewithfeedback/test   0.123s
```

as you can see, debug (all the "***********" print statements) for TestDwfDeploy displays as expected.
however, missing debug (i.e., all the "################" print statements) from the donatewithfeedback_bg.wasm SC itself.

again, the aboe is a test to confirm i'm indeed running correct SC code as expected, but i'm missing/overlooking/incorrect with something.
hoping someone can advise or has explicity steps to follow.  thanks.

