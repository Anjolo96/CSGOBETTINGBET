// ==UserScript==
// @name         CSGODouble Betting BOT
// @namespace    
// @version      1.0
// @description  Getting RICH... Getting kid.
// @author       Maximum Intellectus
// @match        http://www.csgodouble.com/index.php
// @grant        none
// ==/UserScript==
/* jshint -W097 */
'use strict';

///////////////////////////////////////////////////////////
//  ___ __  __ ____   ___  ____ _____  _    _   _ _____  //
// |_ _|  \/  |  _ \ / _ \|  _ \_   _|/ \  | \ | |_   _| //
//  | || |\/| | |_) | | | | |_) || | / _ \ |  \| | | |   //
//  | || |  | |  __/| |_| |  _ < | |/ ___ \| |\  | | |   //
// |___|_|  |_|_|    \___/|_| \_\|_/_/   \_\_| \_| |_|   //
//                                                       //
//    Increment the @version parameter every time you    //
//   update the script (2.0.1, 2.0.2, etc.). Otherwise   //
//   Greasemonkey / Tampermonkey users will NOT update   //
//                    automatically!!                    //
//                                                       //
//    Do not update version number in pull requests;     //
//         It usually causes a merge conflict.           //
//                                                       //
///////////////////////////////////////////////////////////

// Your code here...
 
var initialBetAmount = 15
var mode = 'martingale'; 
var betColor = 'red';
var lastRollColor = "red";
var baseBalance = 0;
var loseStik = 0;
var loseLoop = 0;
var currentBetAmount=initialBetAmount,
    currentRollNumber=1,
    lastStatus,
    lastBetColor,
    lastRollColor,
    $balance=$("#balance"),
    $betAmountInput=$("#betAmount"),
    $statusBar=$(".progress #banner"),
    $redButton=$("#panel1-7 .betButton"),
    $blackButton=$("#panel8-14 .betButton"),
    refreshIntervalId=setInterval(tick,500);
 
function tick(){
    var a=getStatus();
    if(a!==lastStatus&&"unknown"!==a){
        switch(a){case"waiting":bet();
                        break;
                  case"rolled":rolled()
        }lastStatus=a,printInfo()
    }
}
function checkBalance(){
    return getBalance()<currentBetAmount?(
    console.warn("H?t ti?n. Nomore betting."),clearInterval(refreshIntervalId),!1):!0}
function printInfo(){
    var a="\nTr?ng th�i: "+lastStatus+"\nLu?t quay: "+currentRollNumber+"\nM?c cu?c: "+initialBetAmount+"\nTi?n cu?c hi?n t?i: "+currentBetAmount+"\nK?t qu? v�ng tru?c: "+(null===wonLastRoll()?"-":wonLastRoll()?"An ti?n":"T?ch" + "\n FreeCode : CHIMSOLE ");
    if("rolling"!== getStatus)console.log(a)
    }
function rolled(){
    return"anti-martingale"===mode?void antiMartingale():(martingale(),
    void currentRollNumber++,checkStick())
    }
function antiMartingale(){
    if(loseStik < 3){
        currentBetAmount=wonLastRoll()?2*currentBetAmount:initialBetAmount
    }else{
        currentBetAmount=loseLoop*8*initialBetAmount
        }
    }
function martingale(){
    if(loseStik < 3){
        currentBetAmount=wonLastRoll()?initialBetAmount:2*currentBetAmount
    }else{
        currentBetAmount=loseLoop*8*initialBetAmount
    }
     
}
function bet(){
    if(loseStik < 3){
        console.warn("BOT MAX-INT : Happy betting! Keep doing it");
        checkBalance()&&(setBetAmount(currentBetAmount),setTimeout(placeBet,50));
        baseBalance = getBalance();
    }
    else{
        console.warn("BOT MAX-INT : Happy betting! Keep doing it");
    }
    }
function setBetAmount(a){
    $betAmountInput.val(a)
    }
function placeBet(){
    return"red"===betColor?($redButton.click(),void(lastBetColor="red")):($blackButton.click(),void(lastBetColor="black"))
    }
function getStatus(){
    var a=$statusBar.text();
    if(hasSubString(a,"Rolling in"))return"waiting";
    if(hasSubString(a,"***ROLLING***"))return"rolling";if(hasSubString(a,"rolled")){var b=parseInt(a.split("rolled")[1]);
    return lastRollColor=getColor(b),"rolled"}return"unknown"}
function getBalance(){
    return parseInt($balance.text())
    }
function changeColor(){
    if("green"==lastRollColor){
        //Skip
        return"unknown";
    }
    if("red"==lastRollColor){
        betColor = "red"; 
    }else{
        betColor = "black";   
    }
}
function hasSubString(a,b){return a.indexOf(b)>-1}
function checkStick(){
    if(lastBetColor === lastRollColor){
        if(loseStik < 3){
            changeColor();
        }
        loseStik = 0;   
        loseLoop ++;
         
    }else{
        loseStik ++;
    }
    if(baseBalance < getBalance() ){
        console.warn(baseBalance +"<"+getBalance()+" WONNNNNNNNNNNNNN");
        loseLoop = 0;
    }
    console.log("stack "+loseStik + "loop " +loseLoop);
}
function getColor(a){
    return 0==a?"green":a>=1&&7>=a?"red":"black"
    }
function wonLastRoll(){
    return lastBetColor?lastRollColor===lastBetColor:null
    }
