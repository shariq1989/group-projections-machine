<template>
  <v-container>
    <div>
      <div class="inputForm" v-if="!submitted">
        <v-card>
          <v-card-title>Group Projection Machine</v-card-title>
          <v-card-text>
            <!-- Group Creation Form -->
            <v-row>
              <div class="controlFields">
                <v-text-field
                  type="number"
                  v-on:input="numChanged"
                  v-model="groupsNum"
                  id="groupsNumInput"
                  min="2"
                  max="10"
                  label="Groups"
                  outlined
                ></v-text-field>
              </div>
              <div class="controlFields">
                <v-text-field
                  v-on:input="numChanged"
                  v-model.number="teamsNum"
                  type="number"
                  id="teamsNumInput"
                  min="2"
                  max="10"
                  label="Teams per Group"
                  outlined
                />
              </div>
              <div class="controlFields">
                <v-text-field
                  v-model="isPromoted"
                  type="number"
                  id="isPromotedInput"
                  min="2"
                  max="10"
                  label="Promoted Teams per Group"
                  outlined
                />
              </div>
            </v-row>
          </v-card-text>
        </v-card>
        <div class="grid-container" style="padding-top: 0.5em">
          <v-card class="grid-item" style="padding: 1em">
            <v-row v-for="(group, index) in groups" :key="index">
              <v-card-text>Group {{ index + 1 }}</v-card-text>
              <v-col
                cols="12"
                lg="2"
                md="4"
                sm="6"
                v-for="(team, innerIndex) in group"
                :key="innerIndex"
              >
                <v-text-field v-model="team.name" hide-details filled />
              </v-col>
            </v-row>
          </v-card>
        </div>
        <div style="padding: 0.5em">
          <v-btn v-on:click="submit" color="primary">Submit</v-btn>
        </div>
      </div>
      <div v-if="submitted">
        <div>
          <div>
            <v-btn
              v-on:click="back"
              style="margin-bottom: 0.5em"
              color="primary"
              >Back</v-btn
            >
          </div>
          <!-- Group Display -->
          <v-row>
            <v-col
              cols="12"
              lg="4"
              md="6"
              sm="12"
              v-for="(group, index) in groups"
              :key="index"
            >
              <v-card>
                <v-card-title>Group {{ index + 1 }}</v-card-title>
                <v-card-text>
                  <v-simple-table>
                    <thead>
                      <tr>
                        <th>Team</th>
                        <th>Won</th>
                        <th>Drawn</th>
                        <th>Lost</th>
                        <th>Points</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr
                        v-for="(team, innerIndex) in group"
                        :key="innerIndex"
                        v-bind:class="{ promotion: innerIndex < isPromoted }"
                      >
                        <td>
                          {{ team.name }}
                        </td>
                        <td>{{ team.won }}</td>
                        <td>{{ team.drawn }}</td>
                        <td>{{ team.lost }}</td>
                        <td>{{ team.points }}</td>
                      </tr>
                    </tbody>
                  </v-simple-table>
                </v-card-text>
                <div>
                  <!-- Fixtures List -->
                  <v-card-title>Fixtures</v-card-title>
                  <v-card-text>
                    <v-simple-table>
                      <tr
                        style="text-align: left"
                        v-for="(fixture, fixIndex) in fixtures[index]"
                        :key="fixIndex"
                      >
                        <td>
                          <input
                            type="radio"
                            v-bind:id="
                              fixture.t1.id.toString() + fixture.id.toString()
                            "
                            v-bind:name="
                              fixture.t1.id.toString() + fixture.id.toString()
                            "
                            value="0"
                            v-model.number="fixture.res"
                            v-on:click="calculateResults(index)"
                          />
                          <label v-bind:for="fixture.t1.id">{{
                            fixture.t1.name
                          }}</label>
                        </td>
                        <td>
                          <input
                            type="radio"
                            v-bind:id="fixture.id + 'drawn'"
                            v-bind:name="fixture.id + 'drawn'"
                            value="1"
                            v-model.number="fixture.res"
                            v-on:click="calculateResults(index)"
                          />
                          <label for="fixture[1].id-drawn">Drawn</label>
                        </td>
                        <td>
                          <input
                            type="radio"
                            v-bind:id="
                              fixture.t2.id.toString() + fixture.id.toString()
                            "
                            v-bind:name="
                              fixture.t2.id.toString() + fixture.id.toString()
                            "
                            value="2"
                            v-model.number="fixture.res"
                            v-on:click="calculateResults(index)"
                          />
                          <label for="fixture.t2.id">{{
                            fixture.t2.name
                          }}</label>
                        </td>
                      </tr>
                    </v-simple-table>
                  </v-card-text>
                </div>
              </v-card>
            </v-col>
          </v-row>
        </div>
      </div>
      <!--{{ testConsole }}-->
    </div>
  </v-container>
</template>

<script>
function initialState() {
  return {
    teamsNum: 4,
    groupsNum: 2,
    submitted: false,
    fixtures: [],
    groups: [],
    testConsole: [],
    isPromoted: 2,
  };
}
export default {
  name: "GroupMachine",
  data() {
    return initialState();
  },
  created() {
    // reset login status
    this.numChanged();
  },
  methods: {
    numChanged() {
      let totalTeams = this.teamsNum * this.groupsNum;
      let group = 1;
      let counter = 0;
      this.groups = [];
      for (let i = 0; i < totalTeams; i++) {
        if (counter == this.teamsNum) {
          counter = 1;
          group += 1;
        } else {
          counter += 1;
        }
        let teamObj = {
          id: i + 1,
          name: "Team " + (i + 1),
          group: group,
          won: 0,
          drawn: 0,
          lost: 0,
          points: 0,
        };
        if (group > this.groups.length) {
          this.groups.push([]);
          this.groups[group - 1].push(teamObj);
        } else {
          this.groups[group - 1].push(teamObj);
        }
      }
    },
    matchDrawn(t1, t2) {
      t1.drawn += 1;
      t2.drawn += 1;
      t1.points += 1;
      t2.points += 1;
    },
    matchNotDrawn(winner, loser) {
      winner.won += 1;
      winner.points += 3;
      loser.lost += 1;
    },
    findMatchingTeam(groupIndex, teamId) {
      let team = this.groups[groupIndex].find((team) => team.id == teamId);
      return team;
    },
    zeroOutResults(index) {
      for (let team of this.groups[index]) {
        team.won = 0;
        team.lost = 0;
        team.drawn = 0;
        team.points = 0;
      }
    },
    orderTeamsByPoints(groupIndex) {
      this.groups[groupIndex].sort((team1, team2) =>
        team1.points < team2.points ? 1 : -1
      );
    },
    generateFixtures() {
      let id = 0;
      for (let teams of this.groups) {
        let groupArr = [];
        for (let x = 0; x < teams.length - 1; x++) {
          for (let y = x + 1; y < teams.length; y++) {
            groupArr.push({ id: id, res: 1, t1: teams[x], t2: teams[y] });
            id = id + 1;
          }
        }
        this.fixtures.push(groupArr);
      }
    },
    back() {
      Object.assign(this.$data, initialState());
      this.numChanged();
    },
    submit() {
      if (this.submitted) {
        this.submitted = false;
      } else {
        this.submitted = true;
      }
      this.generateFixtures();
      for (let groupId = 0; groupId < this.groupsNum; groupId++) {
        this.calculateResults(groupId);
      }
    },
    calculateResults(groupIndex) {
      // recalculate all fixtures
      // need a timeout because the onchange method gets called before the radio value is set
      setTimeout(() => {
        this.zeroOutResults(groupIndex);
        for (let fixture of this.fixtures[groupIndex]) {
          // team 1 wins
          console.log(groupIndex, fixture, fixture.t1, fixture.t2);
          if (fixture.res == 0) {
            let t1 = this.findMatchingTeam(groupIndex, fixture.t1.id);
            let t2 = this.findMatchingTeam(groupIndex, fixture.t2.id);
            this.matchNotDrawn(t1, t2);
          } else if (fixture.res == 1) {
            let t1 = this.findMatchingTeam(groupIndex, fixture.t1.id);
            let t2 = this.findMatchingTeam(groupIndex, fixture.t2.id);
            this.matchDrawn(t1, t2);
          } else if (fixture.res == 2) {
            let t1 = this.findMatchingTeam(groupIndex, fixture.t1.id);
            let t2 = this.findMatchingTeam(groupIndex, fixture.t2.id);
            this.matchNotDrawn(t2, t1);
          } else {
            console.error("No matching fixture result: " + fixture.res);
            break;
          }
        }
        this.orderTeamsByPoints(groupIndex);
      }, 500);
    },
  },
};
</script>
<style>
.groupTable {
  border-right: 1px solid blue;
  border-bottom: 1px solid blue;
}
.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
}
.grid-item {
  text-align: center;
}
.promotion {
  background: greenyellow;
}
.td {
  align-content: right;
  border: black;
}
.controlFields {
  margin-right: 2em;
}
</style>
