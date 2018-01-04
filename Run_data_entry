/*
I wrote this function to parse run data entered into a sheet in Google. The script expects that the spreadsheet will be formatted as such:
sheet1 = data entry form with a table of values [date, run type (sprint/tempo/long etc), run location (treadmill, outside, etc), 
speed (if running on treadmill)[1], pace (if running outside), distance, reps[2]]

[1]: the script will automatically calculate pace if only speed is given and vice-versa
[2]: if reps is blank, the script assumes you've only done one rep

sheet2 = data log
sheets 3 - N: calendars for each month that you are running formatted as a standard google sheet calendar, plus an extra cell below each day

I have created my own calendar of runs for training for a marathon.

TODO: Implement a cell on the first sheet that will show you today's run distance and calculate a pace based on your average of that run type and goal
TODO: Error notification if data is missing
*/

// helper function to convert pace and speed
function convertPaceSpeed(x){
  var value = ((+x) / 60.0);
  value = Math.pow(value, -1)
  return value
}

// data structure to get the entered data into a manageable form
var data = function(runData){
  this.year = runData[0][0].getYear();
  this.month = runData[0][0].getMonth()+1;
  this.day = runData[0][0].getDate();
  this.type = runData[1][0];
  this.location = runData[2][0];
  
  if(runData[3][0] == ""){
    this.pace = runData[4][0];
    this.speed = convertPaceSpeed(this.pace);
  }
  else{
    this.pace = convertPaceSpeed(runData[3][0]);
    this.speed = runData[3][0];
  }
  
  this.distance = runData[5][0];
  
  if(runData[6][0] == ""){this.reps = 1;}
  else {this.reps = runData[6][0];}
}

// switch function to choose the correct month's sheet - should be updated based on your need case
function chooseSheet(sheets,month){
  switch(month){
    case 1.0:
      return sheets[3];
      break;
    case 2.0:
      return sheets[4];
      break;  
    case 3.0:
      return sheets[5];
      break;  
    case 4.0:
      return sheets[6];
      break;
    case 5.0:
      return sheets[7];
      break; 
    case 12.0:
      return sheets[2]; 
  }
}

// function to get the calendar position of the date in the data entry form
function getPosition(calData, day){
  for (i = 0; i < calData.length; i++){
    for (j = 0; j < calData[i].length; j++){
      if (calData[i][j] == day){
        return [i, j];
        break;
      }
    }
  }
}

// function to turn the information about today's run into a string for logging on the calendar
function squashData(x){
  var toReturn = [];
  var min = Math.floor(x.pace);
  var sec = Math.round((+x.pace % 1)*60);
  if (x.type == "Sprint"){
     toReturn = [min.toString() + ":" + sec.toString() + " " + x.reps.toString() + " x " + x.distance()];
  }
  
  else {
    toReturn = [min.toString() + ":" + sec.toString() + " " + x.distance + "mi."];
  }
  //Logger.log(x.distance.toString());
  return toReturn;
}

// function to put the data to log into an object
function makeArray(object){
  var array = [];
  array.push(object.day + "/" + object.month + "/" + object.year);
  array.push(object.type);
  array.push(object.location);
  array.push(object.speed);
  array.push(Math.floor(object.pace).toString() + ":" + Math.round((+object.pace % 1)*60).toString());
  array.push(object.distance);
  array.push(object.reps);
  return [array];
}

// Main function - I made a button on the data entry sheet to run this upon click
function runProcess() {
  // Get sheet data
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheets = ss.getSheets();
  var dataSheet = sheets[0]; // Where today's run was entered
  var dataLog = sheets[1]; // Where the data should be logged
  var runData = dataSheet.getRange("C2:C8").getValues(); // Today's run data
  var parsedData = new data(runData); // New class containing all of the run data in a structure with useful labels
  
  var cal = chooseSheet(sheets,parsedData.month); // Get the sheet with the correct calendar to log the data
  var calData = cal.getRange("B4:H18").getValues(); // Get the dates in the calendar
  var positions = getPosition(calData, parsedData.day); // Find the position of the day corresponding to the date of Today's run
  
  var cellToWrite = squashData(parsedData); // Make a single array with the value to set in the target cell
  
  var offset = [6, 2]; // offset for the calendar position needed because calendar range is offset from 0,0 when finding positions in getPosition();
  
  cal.getRange(positions[0] + offset[0], positions[1] + offset[1], 1, 1).setValue(cellToWrite); // set the calendar value
  
  var lastRow = dataLog.getLastRow(); // Find the last row with data in the data log
  dataLog.getRange(lastRow + 1,2,1,7).setValues(makeArray(parsedData)); // Log the data from today's run
  
  dataSheet.getRange("C2:C8").clearContent(); // clear the entry form
  //Logger.log(makeArray(parsedData));

}
