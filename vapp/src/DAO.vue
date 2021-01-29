<template>
  <div>
    <div class="container">
      <h1 class="text-center">DAO</h1>

      <p>Shares: {{ shares }}</p>
      <div v-if="activeAccount === admin">
        <!-- <> -->
        <div class="row">
          <div class="col-sm-12">
            <h2>Withdraw ether</h2>
            <form @submit.prevent="withdrawEther">
              <div class="form-group">
                <label For="amount">Amount</label>
                <input
                  type="number"
                  v-model="amount"
                  class="form-control"
                  id="amount"
                />
              </div>
              <div class="form-group">
                <label For="to">To</label>
                <input v-model="to" type="text" class="form-control" id="to" />
              </div>
              <button type="submit" class="btn btn-primary">Submit</button>
            </form>
          </div>
        </div>
        <hr />
        <!-- </> -->
      </div>

      <div class="row">
        <div class="col-sm-12">
          <h2>Contribute</h2>
          <form @submit.prevent="contribute">
            <div class="form-group">
              <label htmlFor="amount">Amount</label>
              <input
                type="number"
                v-model="contr_amount"
                class="form-control"
                id="amount"
              />
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
          </form>
        </div>
      </div>

      <hr />

      <div class="row">
        <div class="col-sm-12">
          <h2>Redeem shares</h2>
          <form @submit.prevent="redeemShares">
            <div class="form-group">
              <label htmlFor="amount">Amount</label>
              <input type="text" class="form-control" id="amount" />
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
          </form>
        </div>
      </div>

      <hr />

      <div class="row">
        <div class="col-sm-12">
          <h2>Transfer shares</h2>
          <form @submit.prevent="transferShares">
            <div class="form-group">
              <label htmlFor="amount">Amount</label>
              <input type="text" class="form-control" id="amount" />
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
          </form>
        </div>
      </div>

      <hr />

      <div class="row">
        <div class="col-sm-12">
          <h2>Create proposal</h2>
          <form @submit.prevent="createProposal">
            <div class="form-group">
              <label htmlFor="name">Name</label>
              <input type="text" class="form-control" id="name" />
            </div>
            <div class="form-group">
              <label htmlFor="amount">Amount</label>
              <input type="text" class="form-control" id="amount" />
            </div>
            <div class="form-group">
              <label htmlFor="recipient">Recipient</label>
              <input type="text" class="form-control" id="recipient" />
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
          </form>
        </div>
      </div>

      <hr />

      <div class="row">
        <div class="col-sm-12">
          <h2>Proposals</h2>
          <table class="table">
            <thead>
              <tr>
                <th>Id</th>
                <th>Name</th>
                <th>Amount</th>
                <th>Recipient</th>
                <th>Votes</th>
                <th>Vote</th>
                <th>Ends on</th>
                <th>Executed</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="proposal in proposals" :key="proposal.id">
                <td>{{ proposal.id }}</td>
                <td>{{ proposal.name }}</td>
                <td>{{ proposal.amount }}</td>
                <td>{{ proposal.recipient }}</td>
                <td>{{ proposal.votes }}</td>
                <td>
                  <span v-if="isFinished(proposal)"> Vote finished </span>
                  <span v-else-if="proposal.hasVoted"> You already voted</span>
                  <button
                    v-else
                    @click.prevent="vote(proposal.id)"
                    type="submit"
                    class="btn btn-primary"
                  >
                    Vote
                  </button>
                </td>

                <td>
                  {{
                    new Date(parseInt(proposal.end) * 1000).toLocaleString()
                  }}}
                </td>
                <td>
                  <span v-if="proposal.executed">Yes</span>
                  <button
                    v-else-if="
                      admin.toLowerCase() === accounts[0].toLowerCase()
                    "
                    @click.prevent="executeProposal(proposal.id)"
                    type="submit"
                    class="btn btn-primary"
                  >
                    Execute
                  </button>
                  <span v-else>No</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  data: () => ({
    shares: undefined,
    proposals: undefined,
    admin: undefined,
    amount: undefined,
    contr_amount: undefined,
    to: undefined,
  }),
  methods: {
    withdrawEther() {
      this.drizzleInstance.contracts.DAO.methods
        .withdrawEther(this.amount, this.to)
        .send();
    },
    contribute() {
      this.drizzleInstance.contracts.DAO.methods
        .contribute()
        .send({ from: this.activeAccount, value: this.contr_amount });
    },
    redeemShares() {},
    transferShares() {},
    createProposal() {},
    executeProposal(id) {},
    init() {
      const contract = this.drizzleInstance.contracts.DAO;
      contract.methods
        .admin()
        .call()
        .then((res) => {
          this.admin = res;
        });
      contract.methods
        .shares(this.activeAccount)
        .call()
        .then((res) => {
          this.shares = res;
        });
    },
  },
  computed: {
    ...mapGetters("accounts", ["activeAccount"]),
    ...mapGetters("drizzle", ["drizzleInstance"]),
  },
  created() {
    this.init();
  },
};
</script>
