<template>

  <v-app class="Lavender">

    <v-content>

      <v-container>

          <v-layout
            text-xs-center
            wrap
          >

            <v-flex mb-4>

              <h1 class="display-2 font-weight-bold mb-3" style="border:25px solid rgb(88, 41, 7);text-align:center;color:black;background-color:FFEBCD;
               opacity:1;padding: 10px;text-align: center;font-size: 25px;">
                Nation Hands Toward Indian Armed Forces
              </h1>




              <h2 class="subheading font-weight-regular" style="font-size: 18px;"><b>
                Making a donation is the ultimate sign of solidarity. Actions speak louder than words.</b>

              </h2>

              <p class="title">

              </p>
            </v-flex>
            </v-layout>



    <v-layout row justify-center>

      <div id="app">
        <div class="title-container">
        <div class="filters">
        <b>
          <span class="filter" v-bind:class="{ active: currentFilter === 'ALL' }" v-on:click="setFilter('ALL')">All Donation Domains</span>
          <span class="filter" v-bind:class="{ active: currentFilter === 'ART' }" v-on:click="setFilter('ART')">Indian Army</span>
          <span class="filter" v-bind:class="{ active: currentFilter === 'WORKSHOPS' }" v-on:click="setFilter('WORKSHOPS')">Air Force</span>
          <span class="filter" v-bind:class="{ active: currentFilter === 'FUN' }" v-on:click="setFilter('DOODLES')">Indian Navy</span>
          </b>
        </div>
      </div>
      </div>

      </v-layout>






	<transition-group class="projects" name="projects" >
		<div class="project" v-if="currentFilter === project.category || currentFilter === 'ALL'" v-bind:key="project.title" v-for="project in projects">
			<div class="project-image-wrapper">
				<img class="project-image" v-bind:src="project.image">
				<div class="gradient-overlay"></div>
				<div class="circle">
					<span class="project-title">{{project.title}}</span>
				</div>
			</div>
		</div>
	</transition-group>
</div>



        <v-layout row justify-center>
          <v-dialog v-model="startProjectDialog" max-width="700px" persistent>
            <v-btn slot="activator" color="DarkKhaki" dark padding:10px font-size="18px"><b>Click Here To Donate</b></v-btn>
            <v-card>
              <v-card-title>
                <span class="headline font-weight-bold mt-2 ml-4" color="Ivory" dark>Let's Help To Nation</span>
              </v-card-title>
              <v-card-text class="pt-0">
                <v-container class="pt-0" grid-list-md>
                  <v-layout wrap>
                    <v-flex xs12>
                      <v-text-field
                        label="Title"
                        persistent-hint
                        v-model="newProject.title">
                      </v-text-field>
                    </v-flex>
                    <v-flex xs12>
                      <v-textarea
                        label="Description"
                        persistent-hint
                        v-model="newProject.description">
                      </v-textarea>
                    </v-flex>
                    <v-flex xs12 sm6>
                      <v-text-field
                        label="Amount Needed (ETH)"
                        type="number"
                        step="0.0001"
                        min="0"
                        v-model="newProject.amountGoal">
                      </v-text-field>
                    </v-flex>
                    <v-flex xs12 sm6>
                      <v-text-field
                        label="Duration (in days)"
                        type="number"
                        v-model="newProject.duration">
                      </v-text-field>
                    </v-flex>
                  </v-layout>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="blue darken-1"
                  flat
                  @click="startProjectDialog = false;
                  newProject.isLoading = false;">
                  Close
                </v-btn>
                <v-btn color="blue darken-1"
                  flat
                  @click="startProject"
                  :loading="newProject.isLoading">
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-layout>
      </v-container>
      </div>
      <v-container
        grid-list-lg
      >

        <h1 class="display-1 font-weight-bold mb-3"style="color:white;font-size: 23px;background-color:#808000;padding: 20px;width: 40%;box-shadow: 10px 10px black;" >
        <b>Donate For this Domains</b>
        </h1>
        <v-layout row wrap>
          <v-flex v-for="(project, index) in projectData" :key="index" xs12>
            <v-dialog
              v-model="project.dialog"
              width="800"
            >
              <v-card>
                <v-card-title class="headline font-weight-bold">
                  {{ project.projectTitle }}
                </v-card-title>
                <v-card-text>
                  {{ project.projectDesc }}
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn
                    color="blue darken-1"
                    flat="flat"
                    @click="projectData[index].dialog = false"
                  >

                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-hover>
              <v-card
                slot-scope="{ hover }"
                :class="`elevation-${hover ? 10 : 2}`"
              >
                <v-card-title primary-title>
                  <div>
                    <div class="headline font-weight-bold">
                      <v-chip
                        label
                        :color="stateMap[project.currentState].color"
                        text-color="white" class="mt-0">
                      {{ stateMap[project.currentState].text }}
                      </v-chip>
                      {{ project.projectTitle }}
                    </div>
                    <br/>
                    <h2><span>{{ project.projectDesc.substring(0, 100) }}</span></h2>
                    <span v-if="project.projectDesc.length > 100">
                      ... <a @click="projectData[index].dialog = true">[Show full]</a>
                    </span>
                    <br/>
                    <h2><small>Up Until: {{ new Date(project.deadline * 1000) }}</small></h2>
                    <br/>
                    <h2><small>Goal of <b style="color:green;">{{ project.goalAmount / 10**18 }}  ETH </b></small></h2>
                    <small v-if="project.currentState == 1">wasn't achieved before deadline</small>
                    <small v-if="project.currentState == 2">has been achieved</small>
                  </div>
                </v-card-title>
                <v-flex
                  v-if="project.currentState == 0 && account != project.projectStarter"
                  class="d-flex ml-3" xs12 sm6 md3>
                  <v-text-field
                    label="Amount (in ETH)"
                    type="number"
                    step="0.0001"
                    min="0"
                    v-model="project.fundAmount"
                  ></v-text-field>
                  <v-btn
                    class="mt-3"
                    color="light-green darken-1 white--text"
                    @click="fundProject(index)"
                    :loading="project.isLoading"
                  >
                    Fund
                  </v-btn>

                </v-flex>
                <v-flex class="d-flex ml-3" xs12 sm6 md3>
                  <v-btn
                    class="mt-3"
                    color="amber darken-1 white--text"
                    v-if="project.currentState == 1"
                    @click="getRefund(index)"
                    :loading="project.isLoading"
                  >
                    Get refund
                  </v-btn>
                </v-flex>
                <v-card-actions v-if="project.currentState == 0" class="text-xs-center">
                  <span class="font-weight-bold" style="width: 200px;">
                    {{ project.currentAmount / 10**18 }} ETH
                  </span>
                  <v-progress-linear
                    height="10"
                    :color="stateMap[project.currentState].color"
                    :value="(project.currentAmount / project.goalAmount) * 100"
                  ></v-progress-linear>
                  <span class="font-weight-bold" style="width: 200px;">
                    {{ project.goalAmount / 10**18 }} ETH
                  </span>
                </v-card-actions>
              </v-card>
            </v-hover>
          </v-flex>
        </v-layout>

      </v-container>

    </v-content>

  </v-app>

</template>

<script>
// We import our the scripts for the smart contract instantiation, and web3



import crowdfundInstance from '../contracts/crowdfundInstance';
import crowdfundProject from '../contracts/crowdfundProjectInstance';
import web3 from '../contracts/web3';






export default {
  name: 'App',
  data() {
    return {

    color: '#DCDCDC',
    currentFilter: 'ALL',
		projects: [
			{title: "army school", image: "http://static-ai.asianetnews.com/images/01dbc9sb46dpdjtymp4j86hsat/Scholarships.jpg", category: 'ART'},
			{title: "donate to NDF", image: "https://www.ssbcrack.com/wp-content/uploads/2015/06/236172-nda-army-1024x1024.jpg", category: 'ART'},
			{title: "For Medicines", image: "http://news.euttaranchal.com/wp-content/uploads/Indian-army-medical.jpg", category: 'ART'},
      {title: "For food", image: "https://www.samaa.tv/wp-content/uploads/2019/03/AFP-10.jpg", category: 'ART'},
      {title: "For Weapons", image: "https://new-img.patrika.com/upload/images/2016/02/28/indian-army-weapons-1456640881_835x547.jpg", category: 'ART'},
			{title: "Navi schools", image: "https://2.bp.blogspot.com/-K1MnOqQqh5k/WVdYeZp-9rI/AAAAAAAAJLU/N0dzv9U3v2QLaf3BdI6ke1E5NLJ6nwJuQCLcBGAs/s640/Indian-Navy-Cadets.jpg", category: 'DOODLES'},
			{title: "for medicines", image: "https://www.indiannavy.nic.in/sites/default/files/news/Medi-Capm-Kochi_Pix4-M.JPG", category: 'DOODLES'},
      {title: "for food", image: "https://i.ndtvimg.com/i/2017-08/indian-navy-food-mumbai-twitter_650x400_51504071580.jpg", category: 'DOODLES'},
      {title: "for weapons", image: "https://www.ssbcrack.com/wp-content/uploads/2015/10/Indian-Navy-UES-2016.jpg", category: 'DOODLES'},
			{title: "Air Force School", image: "https://media.defense.gov/2017/Mar/16/2001717503/780/780/0/170308-F-LD740-002.JPG", category: 'WORKSHOPS'},
      {title: "for Equipments", image: "https://gumlet.assettype.com/swarajya%2F2019-02%2Fd11eaf32-222a-4d50-9ea7-d4ae037f100a%2FDassault_Mirage_2000.jpg?rect=0%2C53%2C600%2C338&w=480&q=75&auto=format%2Ccompress", category: 'WORKSHOPS'},
      {title: "for Medicines", image: "https://media.defense.gov/2012/Nov/14/2000096795/780/780/0/121114-F-SR682-005.JPG", category: 'WORKSHOPS'},
			{title: "for food", image: "https://aimhigherin.com/wp-content/uploads/2020/01/1000w_q95.jpg", category: 'WORKSHOPS'},
      {title: "For Weapons", image: "https://cdn.defenseone.com/media/img/cd/2017/11/15/37882240451_439c8bc151_o/860x394.jpg?1618268458", category: 'WORKSHOPS'},
		],


      startProjectDialog: false,
      account: null,
      stateMap: [
        { color: 'orange', text: 'Ongoing' },
      ],
      projectData: [],
      newProject: { isLoading: false },
    };
  },

  mounted() {
    // this code snippet takes the account (wallet) that is currently active
    web3.eth.getAccounts().then((accounts) => {
      [this.account] = accounts;
      this.getProjects();
    });
  },
  methods: {
    setFilter: function(filter) {
			this.currentFilter = filter;
		},
    getProjects() {
  crowdfundInstance.methods.returnAllProjects().call().then((projects) => {
    projects.forEach((projectAddress) => {
      const projectInst = crowdfundProject(projectAddress);
      projectInst.methods.getDetails().call().then((projectData) => {
        const projectInfo = projectData;
        projectInfo.isLoading = false;
        projectInfo.contract = projectInst;
        this.projectData.push(projectInfo);
      });
    });
  });
},
    startProject() {
  this.newProject.isLoading = true;
  crowdfundInstance.methods.startProject(
    this.newProject.title,
    this.newProject.description,
    this.newProject.duration,
    web3.utils.toWei(this.newProject.amountGoal, 'ether'),
  ).send({
    from: this.account,
  }).then((res) => {
    const projectInfo = res.events.ProjectStarted.returnValues;
    projectInfo.isLoading = false;
    projectInfo.currentAmount = 0;
    projectInfo.currentState = 0;
    projectInfo.contract = crowdfundProject(projectInfo.contractAddress);
    this.startProjectDialog = false;
    this.newProject = { isLoading: false };
  });
},
    fundProject(index) {
  if (!this.projectData[index].fundAmount) {
    return;
  }

  const projectContract = this.projectData[index].contract;
  this.projectData[index].isLoading = true;
  projectContract.methods.contribute().send({
    from: this.account,
    value: web3.utils.toWei(this.projectData[index].fundAmount, 'ether'),
  }).then((res) => {
    const newTotal = parseInt(res.events.FundingReceived.returnValues.currentTotal, 10);
    const projectGoal = parseInt(this.projectData[index].goalAmount, 10);
    this.projectData[index].currentAmount = newTotal;
    this.projectData[index].isLoading = false;

    // Set project state to success
    if (newTotal >= projectGoal) {
      this.projectData[index].currentState = 2;
    }
  });
},
    getRefund(index) {
  this.projectData[index].isLoading = true;
  this.projectData[index].contract.methods.getRefund().send({
    from: this.account,
  }).then(() => {
    this.projectData[index].isLoading = false;
  });
},
  },
};

</script>
<style>
html,body {
	margin:5;
	font-family: Arial, Helvetica, sans-serif;
   background-image:url('https://tse1.mm.bing.net/th?id=OIP.vjT9_PdGKWIlXg7Qy_bk_AHaFC&pid=Api&P=0&w=293&h=200');
   background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: cover;
},

.title-container {
	display:flex;
	flex-direction:column;
	justify-content:center;
	align-items:center;
  box-shadow:2px gray;
  border:2px black;
}




.title {
	font-family: Arial, Helvetica, sans-serif;
	font-size:35pt;
	font-weight:normal;
}

.project-title {
font-size:14pt
}

.filter {
	font-family:arial;
	padding: 8px 8px;
	cursor:pointer;
	border-radius: 6px;
	transition: all 0.35s;
  background-color: #FFF8DC;
  width: 30px;
  border: 2px solid green;
  font-size:18px;
  margin: 2px;
}

.filter.active {
	box-shadow:0px 1px 3px 0px #00000026;
}

.filter:hover {
	background:#808080;
}

.projects {
	margin-bottom:50px;
	margin-top:25px;
	display:flex;
	flex-wrap:wrap;
	justify-content:center;
}

.projects-enter {
	transform: scale(0.5) translatey(-80px);
	opacity:0;
}

.projects-leave-to{
	transform: translatey(30px);
	opacity:0;
}

.projects-leave-active {
	position: absolute;
	z-index:-1;
}

.circle {
	text-align:center;
	position:absolute;
	bottom:-40px;
	left:40px;
	width:85px;
	height:75px;
	border-radius:50px;
/* 	border:1px solid black; */
	display:flex;
	box-shadow: 0px -4px 3px 0px #494d3257;
	justify-content:center;
	align-items:center;
	background-color:#FFFFFF;
/* 	box-shadow:0px -3px 3px #484848a6; */
}



.project {
	transition: all .35s ease-in-out;
	margin:10px;
	box-shadow:0px 2px 8px lightgrey;
	border-radius:3px;
	width:180px;
	height:200px;
	display:flex;
	flex-direction:column;
	align-items:center;
}

.project-image-wrapper {
	position:relative;
}

.gradient-overlay {
	position:absolute;
	top:0;
	left:0;
	width:100%;
	height:150px;
	opacity:0.09;
	background:
		linear-gradient(to bottom, rgba(0,210,247,0.65) 0%,rgba(0,210,247,0.64) 1%,rgba(0,0,0,0) 100%),
		linear-gradient(to top, rgba(247,0,156,0.65) 0%,rgba(247,0,156,0.64) 1%,rgba(0,0,0,0) 100%);
	border-bottom-left-radius:10px;
	border-bottom-right-radius:10px;
	border-top-left-radius:3px;
	border-top-right-radius:3px;
}

.project-image {
	width:100%;
	height:150px;
	border-bottom-left-radius:5px;
	border-bottom-right-radius:5px;
	border-top-left-radius:3px;
	border-top-right-radius:3px;
}
</style>
