
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello World</title>
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>

    <!-- Don't use this in production: -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <link rel="stylesheet" type="text/css" href="ReactTimer.css">
  </head>
  <body>
    <div id="root"></div>
    <script type="text/babel">

      /*
// <Timeline>
// tick() updates state.time each second

*/

/* NEXT TO DO - TIMER that BOYS USE
// indiv start btns + update end time
// audio (siri?) nearly and halfway notif  file:///Users/aledworkarea/Desktop/timer-sounds/Air%20Wrench.m4a
// add/remove
// group?
// prep during - mvp sequential
// UI - keep next prominent
// splash + polish
// spare time
// learn


*/

/* audio
<audio id="myAudio" >
  <source src="horse.ogg" type="audio/ogg">
  <source src="horse.mp3" type="audio/mpeg">
  Your browser does not support the audio element.
</audio>

<p>Click the button to find out if the audio controls are displayed.</p>

<button onclick="document.getElementById('myAudio').play()">Try it</button>



*/


const recipe = [
  {
    prepMins: 2,
    prepWords: "Wash potatoes, onto baking tray",
    preheatMins: 3,
    mins: 90,
    action: "Baked potatoes",
    cook: "oven"
  },
  {
    prepMins: 2,
    prepWords: "Spread vegetables over oiled, foiled baking tray, drizzle oil on top",
    preheatMins: 6,
    mins: 36,
    action: "Roast vegetables",
    cook: "oven"
  },
  {
    prepMins: 8,
    prepWords:
      "Lay salmon on oiled, foiled baking tray, season, cover with crust mixture",
    preheatMins: 6,
    mins: 22,
    action: "Crusted salmon",
    cook: "oven"
  },
  {
    prepMins: 2,
    prepWords: "Wash and cut broccoli",
    preheatMins: 5,
    preheatWords: "Get pan of water simmering",
    mins: 10,
    action: "Broccoli",
    cook: "saucepan"
  }
]; // each step also gets startTime and endTime
//--------------------------------------------------

//================ TIMELINE ============================
//

class Timeline extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      go: false,
      time: new Date(), // update each second by tick();
      recipe: recipe,
      targetEatTime: this.formatTargetEatTimestampFromTime('18:00'),
      ovenIsOn : 0,
      onPlease : 0
    };
  }



  tick() {
    this.setState({
      time: new Date()
    });
  }
  componentWillMount() {
    // set endTimes from default eatTime (here to avoid loop)
    this.handleTargetTimeInput(this.state.targetEatTime);
  }
  componentDidMount() {
    this.timerID = setInterval(() => this.tick(), 1000);
  }
  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  handleOvenOnPlease(showHide) {
    console.log('handleOvenOnPlease FIRED ');
    console.log('handleOvenOnPlease '+ this.state.ovenIsOn +' '+ this.state.onPlease);
    // wrong (because async prob): this.setState({ ovenIsOn : 1,  onPlease : 1 });
    // correct:
    showHide == 'hide' ?
    this.setState((state, props) => ({
      ovenIsOn : 1 ,
      onPlease : 0
      }))
    :
    this.setState((state, props) => ({
      ovenIsOn : 1 ,
      onPlease : 1
      }))

  }


  handleTargetTimeInput(tt) {
      //// TODO simplify - update state, then let others read state
    ////console.log('handleTargetTimeInput');
    for (let j = 0; j < this.state.recipe.length; j++) {
      let recipe = this.state.recipe;
      // set end of COOKING times as timestamps
      recipe[j].endTime = tt - 0; // adding '-0' stops it being string
      // set COOKING start times as TIMESTAMPS (step handles other phases)
      let stepCookTime = recipe[j].mins *1000 *60;
      recipe[j].startTime = recipe[j].endTime - stepCookTime;
      // save as state
      this.setState({
        recipe: recipe,
        targetEatTime:  tt - 0
      });
      ////console.log(this.state.recipe);
    }
  }
  handleActionInput(e, s) {
    let recipe = this.state.recipe;
    let which = recipe.indexOf(s);
    recipe[which].action = e.target.value;
    this.setState({ recipe });
  }
  handleMinsInput(e, s) {
    let recipe = this.state.recipe;
    let which = recipe.indexOf(s);
    recipe[which].mins = e.target.value - 0; // hack stops type problem
    this.setState({ recipe });
  }
  handleCookSelect(e, s) {
    let recipe = this.state.recipe;
    let which = recipe.indexOf(s);
    recipe[which].cook = e.target.value;
    this.setState({ recipe });
  }
  handleRemoveDish(e, s) {
    let recipe = this.state.recipe;
    let which = recipe.indexOf(s);
    if(which != -1) {  recipe.splice(which, 1); }
    this.setState({ recipe });
  }


  formatTargetEatTimestampFromTime(t){
        // e.g. t = 18:00
        let d = new Date();
      let dd = d.getDate();
      let m = d.getMonth();
      let months = new Array('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec');
      let today = dd+'-'+months[m]+'-2020 ';
      return (Date.parse(today + t) ); // eg 1597770000000
  }

  formathhmmFromTimestamp(t){
    let d = new Date(t);
    let dh = d.getHours();
    let ampm = dh > 11 ? 'pm' : 'am';
    let h = dh > 12 ? dh - 12 : dh; // 6pm not 18pm
    let m = d.getMinutes();
    let mm = m>9 ? m : '0'+m; // 6:03 not 6:3
    mm = (mm==0)? '' : ':'+mm; // 6pm, not 6:00pm

    return (h+ mm + ampm);
  }

   secsUntilStart(step){
     let secsUntilStart = Math.floor((step.startTime - this.state.time) / 1000);
      return secsUntilStart;
    }
  secsUntilEnd(step){
      return (step.endTime - this.state.time) / 1000;
    }
  secsUntilPrep(step){
    let prepMillisecs = step.prepMins * 60*1000;
    let secsUntilPrep = Math.floor(((step.startTime - this.state.time) - prepMillisecs) / 1000);
      return secsUntilPrep;
    }



  render() {



    // ============= AFTER START IS CLICKED ================
    if (this.state.go) {
      const listItems = this.state.recipe.map((step) => (

        <Step
          key={step.action} // TODO index?
          prepMins={step.prepMins}
          prepWords={step.prepWords}
          preheatMins={step.preheatMins}////
          preheatWords={step.preheatWords}////
          mins={step.mins}
          action={step.action}
          startTime={step.startTime}
          endTime={step.endTime}
          cook={step.cook}
          go={this.state.go}
          secsUntilStart={this.secsUntilStart(step)}
          secsUntilEnd={this.secsUntilEnd(step)}
          secsUntilPrep={this.secsUntilPrep(step)}
          handleOvenOnPlease={()=>this.handleOvenOnPlease()}
          ovenIsOn= {this.state.ovenIsOn}////

        />
      ));


     ////console.log('this.state.warning '+this.state.warning);

      return (
        <div className="timeline">


          {this.state.onPlease ?
            <div
             className='warning'
             onClick = {()=>this.handleOvenOnPlease('hide')}
             >
             Oven on now, please
             <div className=' prompt'>Done</div>
            </div>
          : null}

          {listItems}

        <button
            className="back"
            onClick={() =>
              this.setState({
                go: false
              })
            }
          >
            back
          </button>
        </div>
      );
    }

    // ============= BEFORE START ================
    if (!this.state.go) {


      const listSummary = this.state.recipe.map((step) => (
        //// steps as form
        <Step
          key={step.index}
          mins={step.mins}
          action={step.action}
          cook={step.cook}
          handleActionInput={(e) => {
            this.handleActionInput(e, step);
          }}
          handleMinsInput={(e) => {
            this.handleMinsInput(e, step);
          }}
          handleCookSelect={(e) => {
            this.handleCookSelect(e, step);
          }}
          handleRemoveDish={(e) => {
            this.handleRemoveDish(e, step);
          }}
        />
      ));

      // calc [time] get started by [e.g. 5:50pm]
      const r0=this.state.recipe[0];
      let longestTotalMins = null;
      if (r0.prepMins > r0.preheatMins) {longestTotalMins = r0.mins + r0.prepMins}else{longestTotalMins = r0.mins + r0.preheatMins}; //// TODO check all this
      let clockTimeToGetStartedBy = this.state.targetEatTime - (longestTotalMins *60*1000);

      //calc earliest feasible eating time

      let earliestTargetEatTime = Date.parse(this.state.time) + (longestTotalMins *60*1000)




      return (
        <div>
          <div className="timeline">
            <Title main={"hi"} recipe={this.state.recipe} />
            <div className="startTimeOptions">
              Get started by {this.formathhmmFromTimestamp(clockTimeToGetStartedBy)} (latest) to eat at{" "}
              <select onChange={(e) => this.handleTargetTimeInput(e.target.value)}>

                <option value={this.state.targetEatTime}>{this.formathhmmFromTimestamp(this.state.targetEatTime) }
                </option>
                <option value={earliestTargetEatTime}> {this.formathhmmFromTimestamp(earliestTargetEatTime)} (asap!)</option>
                <option value={this.formatTargetEatTimestampFromTime('18:00:00')}>6pm</option>
                <option value={this.formatTargetEatTimestampFromTime('19:00:00')}>7:00pm</option>
                <option value={this.formatTargetEatTimestampFromTime('19:15:00')}>7:15pm</option>
                <option value={this.formatTargetEatTimestampFromTime('19:30:00')}>7:30pm</option>
                <option value={this.formatTargetEatTimestampFromTime('19:45:00')}>7:45pm</option>
                <option value={this.formatTargetEatTimestampFromTime('20:00:00')}>8pm</option>
                <option value={this.formatTargetEatTimestampFromTime('20:15:00')}>8:15pm</option>
              </select>{" "}

            </div>
            <button
              onClick={() => {
                  this.setState({ go: true });
                }
              }>Get started</button>
            <h4>Adjust cooking times here:</h4>
            {listSummary}
          </div>
        </div>
      );
    }

  }
} // end Timeline ---------------------------
//================ STEP ============================
//
class Step extends React.Component {
  // Time display formatting -------------
  addZeros(num) {
    return (num > 9 ? num : "0" + num) /* 3:05 not 3:5 */
  }
  formatTime(secs) {
    let ss = Math.floor(secs % 60);
    let mm = Math.floor(secs / 60);
    let hh = Math.floor(mm % 60);
    return  mm + ":" + this.addZeros(ss); /* 23:05 */
  }
  formatTimeMinsOnly(secs) {
    // return Math.floor(secs / 60) + " mins"; /* 4 mins */
    let m = Math.ceil(secs / 60);
     let mm = Math.ceil(m % 60);
     let h = Math.floor(m / 60);
     let hh= h > 0 ? (h + "hr ")  + this.addZeros(mm) + ' mins': mm + ' mins';
    //let hh= h > 0 ? (h + "hr ") : '';
     //return  (h > 0 ? hh + this.addZeros(mm) + ' mins' : + this.addZeros(mm) + ' mins')  ;
    return  hh;
  }

  // -end time formatting ---


  render() {
    let secsUntilStart= this.props.secsUntilStart;
    let secsUntilEnd= this.props.secsUntilEnd;
    let secsUntilPrep= this.props.secsUntilPrep;


    /// ===== BEFORE 'go' = true, show form ==============
    // form - SEE DOCS https://reactjs.org/docs/forms.html
    if (!this.props.go) {
      return (
        <div className="step summary">
          {" "}
          <input
            type="text"
            className="action"
            value={this.props.action}
            onChange={(e) => this.props.handleActionInput(e)}
          ></input>
          {" "}need{" "}

          <input
            type="number"
            min="1"
            max="999"
            className="mins"
            value={this.props.mins}
            onChange={(e) => this.props.handleMinsInput(e)}
          ></input>{" "}
          mins in{" "}
          <select
            onChange={(e) => this.props.handleCookSelect(e)}
            >
            <option value={this.props.cook}>{this.props.cook}</option>
            <option value="oven">oven</option>
            <option value="saucepan">saucepan</option>
            <option value="grill">grill</option>
</select>
          <button
            className='x'
            onClick = {(e)=>this.props.handleRemoveDish(e)}
            >x</button>
        </div>
      );
    } // -------------

    /// ===== after start, go = true ==============


    // preheat - TURN OVEN ON
    if (secsUntilStart < this.props.preheatMins * 60) {
      // ask user to preheat oven if needed
      if ((!this.props.ovenIsOn )&&(this.props.cook == 'oven')) {
        this.props.handleOvenOnPlease();
      } ;
    }

    // notyet
    if (secsUntilStart > this.props.prepMins * 60) {
      return (
        <div className="step notyet">
          <div>
            <div>In about {this.formatTimeMinsOnly(secsUntilPrep)}</div>
            <div className="hilite">{this.props.prepWords}</div>
          </div>
          <div>
            {secsUntilStart < (10 * 60)? <><span className="strong">Don't start cooking</span> for {this.formatTimeMinsOnly(secsUntilStart)}</> : null}
          </div>
        </div>
      );
    }


    // getready
    if (secsUntilStart > 0) {
      return (
        <div className="step getready">
          {this.props.prepWords}
          <div className="faint">Get ready to cook in </div>
          <div className="bigTime">{this.formatTime(secsUntilStart)}</div>
        </div>
      );
    }

    // started
    if (secsUntilStart <= 0 && secsUntilEnd > 0) {
      return (
        <div className="step started">
          {this.props.action}: <span className="faint"> cooking now</span>
          <div className="bigTime">{this.formatTime(secsUntilEnd)}</div>
        </div>
      );
    }

    // ended
    if (secsUntilEnd <= 0) {
      return (
        <div className="step ended">
          {this.props.action}:<div className="bigTime">done</div>
        </div>
      );
    }

    // error
    return (
      <div>
        {" "}
        ERROR:Other case: {this.props.action} in {this.formatTime(secsUntilStart)}
      </div>
    );
  }

} // end Step -----------------------------------
//================ TITLE ============================
//
const Title = (props) => {
  //// TODO - make dynamic, make main dish settable eg salmon, not baked potatoes
  return (
    <div className="title">
      <span className="faint">Cook </span>
      {props.recipe[0].action}
      <span className="faint"> with </span>
      {props.recipe[1] ? props.recipe[1].action : null}
      <span className='faint'>, </span>
      {props.recipe[2] ? props.recipe[2].action : null}
      {props.recipe[3] ? <span className="faint"> and </span>  : null}
      {props.recipe[3] ? props.recipe[3].action : null}
    </div>
  );
}; // end Title --------------------------------------------------


// ===========
ReactDOM.render(<Timeline />, document.getElementById("root"));


    </script>
    <!--
      Note: this page is a great way to try React but it's not suitable for production.
      It slowly compiles JSX with Babel in the browser and uses a large development build of React.

      Read this section for a production-ready setup with JSX:
      https://reactjs.org/docs/add-react-to-a-website.html#add-jsx-to-a-project

      In a larger project, you can use an integrated toolchain that includes JSX instead:
      https://reactjs.org/docs/create-a-new-react-app.html

      You can also use React without JSX, in which case you can remove Babel:
      https://reactjs.org/docs/react-without-jsx.html
    -->
  </body>
</html>
