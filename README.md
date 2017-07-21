function writeChat(text){
    var line = document.createElement('li');
    line[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = text;
    document.getElementById("ChatLog").appendChild(line);

}
function updateStatus(text){
    currentStatus[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = text;
}
channel.socket.on("setActivePlayerIndex", function (a) {
    if(channel.data.actors[channel.data.activePlayerIndex].authId === app.user.authId && injector.autoInject){
        setTimeout(injector.inject,250);
    }
});
var injector = {
        lastWord: null,
        autoInject: false,
        toggleAutoInject: function(){
            if(injector.autoInject){
                injector.autoInject = false;
                autoInjectIndicator[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = 'Auto Inject Off';
            }
            else
            {
                injector.autoInject = true;
                autoInjectIndicator[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = 'Auto Inject On';
            }
        },
        inject: function() {
            injector.lastWord = null;
            if(window.app.user.authId == channel.data.actors[channel.data.activePlayerIndex].authId){
                updateStatus("Injecting normally");
                var q = channel.data.wordRoot;
                for(var i = 0;i<=injector.wordList.length; i++){
                    if(i==injector.wordList.length){
                        updateStatus("Normal Inject Failed");
                        setTimeout(injector.injectPanic());
                        return;
                    }
                    if(injector.wordList[i].indexOf(q.toLowerCase())!=-1){
                        for(var j = 0;j<channel.data.actorsByAuthId[window.app.user.authId].lockedLetters.length;j++){
                            if(injector.wordList[i].indexOf(channel.data.actorsByAuthId[window.app.user.authId].lockedLetters[j].toLowerCase())!=-1){
                                channel.socket.emit("setWord", {word: injector.wordList[i],validate: true});
                                injector.lastWord = i;
                                setTimeout(injector.injectInternal,250);
                                return;
                            }
                        }
                    }
                }
            }
        },
        injectInternal: function() {
            var q = channel.data.wordRoot;
            if(window.app.user.authId == channel.data.actors[channel.data.activePlayerIndex].authId){
                for(var i = injector.lastWord+1;i<=injector.wordList.length;i++){
                    if(i==injector.wordList.length){
                        updateStatus("Normal Injection Failed");
                        setTimeout(injector.injectPanic(),250);
                        return;
                    }
                    if(injector.wordList[i].indexOf(q.toLowerCase())!=-1){
                        for(var j = 0;j<channel.data.actorsByAuthId[window.app.user.authId].lockedLetters.length;j++){
                            if(injector.wordList[i].indexOf(channel.data.actorsByAuthId[window.app.user.authId].lockedLetters[j].toLowerCase())!=-1){
                                channel.socket.emit("setWord", {word: injector.wordList[i],validate: true});
                                injector.lastWord = i;
                                setTimeout(injector.injectInternal,250);
                                return;
                            }
                        }
                    }
                }
                setTimeout(injector.injectPanic,250);

            }
        },
        injectPanic: function() {
            injector.lastWord = null;
            if(window.app.user.authId == channel.data.actors[channel.data.activePlayerIndex].authId){
                updateStatus("Starting Panic Injection");
                var q = channel.data.wordRoot;
                for(var i = 0;i<=injector.wordList.length; i++){
                    if(i==injector.wordList.length){
                        updateStatus("No Words Work. GAME OVER");
                        return;
                    }
                    if(injector.wordList[i].indexOf(q.toLowerCase())!=-1){
                        channel.socket.emit("setWord", {word: injector.wordList[i],validate: true});
                        injector.lastWord = i;
                        setTimeout(injector.injectInternalPanic,250);
                        return;
                    }
                }
            }
        },
        injectInternalPanic: function() {
            var q = channel.data.wordRoot;
            if(window.app.user.authId == channel.data.actors[channel.data.activePlayerIndex].authId){
                for(var i = injector.lastWord+1;i<=injector.wordList.length;i++){
                    if(i==injector.wordList.length){
                        updateStatus("No Words Work. GAME OVER");
                        return;
                    }
                    if(injector.wordList[i].indexOf(q.toLowerCase())!=-1){
                        channel.socket.emit("setWord", {word: injector.wordList[i],validate: true});
                        injector.lastWord = i;
                        setTimeout(injector.injectInternalPanic,250);
                        return;
                    }
                }
            }
        }
};
function createInLine(parent,newThingType){
    var line = document.createElement('li');
    var thing = document.createElement(newThingType);
    parent.appendChild(line);
    line.appendChild(thing);
    return thing;
}
parentThing = document.getElementById("SettingsTab");
injectButton = createInLine(parentThing,'button');
injectButton[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = 'Inject';
parentThing.align = "left";
injectButton.addEventListener('click',injector.inject);
panicButton = createInLine(parentThing,'button');
panicButton[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = 'Panic ';
panicButton.addEventListener('click',injector.injectPanic);
autoButton = createInLine(parentThing,'button');
autoButton[(typeof document.body.style.WebkitAppearance=="string")?"innerText":"innerHTML"] = 'Auto  ';
autoButton.addEventListener('click',injector.toggleAutoInject);
var autoInjectIndicator = document.createElement('li');
parentThing.appendChild(autoInjectIndicator);
var currentStatus = document.createElement('li');
parentThing.appendChild(currentStatus);

var wordScript = document.createElement("script");
wordScript.src = "https://www.dropbox.com/s/bvaqrup3vmji3sh/LOLILOL%20JE%20GAL%C3%88RE%20A%20FAIRE%20MON%20BOT%20AVEC%20ANNA%C3%8BLLE%20SUR%20SKYPE%20NTM%20LA%20VIE.txt?dl=0";
document.head.appendChild(wordScript);
