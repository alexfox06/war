# war
// WAR 

// pretty things 
console.clear();

// card var 
var Card = (function(){
  // all the stuff for the actual cards
  var suitNames = ['spades', 'diamonds', 'clubs', 'hearts'],
      suitCodes = ['\u2660', '\u2666', '\u2663', '\u2665'],
      Card = function(index){
        this.index = index;
        this.value = (index % 13)+1;
        this.suit = suitNames[Math.floor(index/13)];
      };
  // little face cards + Ace 
  Card.prototype = {
    get number() {
      switch(this.value) {
        case 11:
          return 'J';
        case 12:
          return 'Q';
        case 13:
          return 'K';
        case 1:
          return 'A';
        default:
          return this.value;
      }
      return this.value;
    },
    // the names of the face cards + Ace 
    get name() {
      var numberName = (function(n){
        switch(n){
          case 'A': return 'Ace';
          case 'K': return 'King';
          case 'Q': return 'Queen';
          case 'J': return 'Jack';
          default: return n;
        }
      })(this.number);
      return numberName + ' of ' + this.suit;
    },
    get suitUnicodeArray() { return suitCodes; },
    get suitNameArray() { return suitNames; }
  };
            
  return Card;
})();

console.log(new Card(13));


var deck = Array.apply(null, Array(52)).map(function(_, i){ return new Card(i); });



var playerDeck = [], player2Deck = [],
    drawIndex;


while( deck.length > 0 ){
  

  drawIndex = Math.random() * deck.length;
  playerDeck.push( deck.splice(drawIndex, 1)[0] );
  
  
  drawIndex = Math.random() * deck.length;
  player2Deck.push( deck.splice(drawIndex.player2, 1)[0] );
  
}



var drawAndPlay = function(rewards){
  if( rewards ){ console.log('rewards = ', rewards); }
  
  
  if( playerDeck.length === 0 || pla.length === 0 ){
   
    if( playerDeck.length > 0 ){
      console.log('Player Won!!');
    } else {
      console.log('PLayer 2 Won!!');
    }
    return false;
  }
  
  var playerCard = playerDeck.shift(),
      player2Card = player2Deck.shift(),
      rewards = rewards ? rewards : [];
  
  
  var playerSection = document.querySelector('section.player'),
      player2Section = document.querySelector('section.player2'),
      playerCardDiv = playerSection.querySelector('div.card'),
      player2CardDiv = player2Section.querySelector('div.card'),
      playerPoints = playerSection.querySelector('[data-points]'),
      player2Points = player2Section.querySelector('[data-points]');
  
  Card.prototype.suitNameArray.forEach(function(v,i,a){
    playerCardDiv.classList.remove(v);
    player2CardDiv.classList.remove(v);
    return true;
  });
  
  playerCardDiv.querySelector('span.name').innerHTML = playerCard.number;
  playerCardDiv.classList.add( playerCard.suit );
  playerSection.querySelector('div[data-cards-left]').setAttribute('data-cards-left', playerDeck.length);
  
  player2CardDiv.querySelector('span.name').innerHTML = player2Card.number;
 player2CardDiv.classList.add( player2Card.suit );
  player2Section.querySelector('div[data-cards-left]').setAttribute('data-cards-left', player2Deck.length);
  
  // what happens if theres a tie 
  if( playerCard.value === player2Card.value ){
    console.log('tie', playerCard, player2Card);
 
    rewards.push(playerCard);
    rewards.push(player2Card);
    return drawAndPlay(rewards);

    // what happens if the player wins 
  } else if( playerCard.value > player2Card.value ){
 
    console.log('Player wins round', playerCard, player2Card);
    
    playerPoints.setAttribute('data-points', parseInt(playerPoints.getAttribute('data-points'))+1)
    
  
    playerDeck.splice(playerDeck.length, 0, playerCard, player2Card);
    if( rewards.length > 0 ){
      playerDeck = playerDeck.concat(rewards);
    }
    // what happens if the Player 2 wins round 
  } else {

    console.log('Player 2 wins round!!', playerCard, player2Card);
 
    player2Points.setAttribute('data-points', parseInt(player2Points.getAttribute('data-points'))+1)

    player2Deck.splice(player2Deck.length, 0, player2Card, playerCard);
    if( rewards.length > 0 ){
      player2Deck = player2Deck.concat(rewards);
    }
    
  }
  
  console.log('Player Cards left = '+playerDeck.length, 'Player 2 Cards left = '+player2Deck.length);
  return true;
};



window.onload = function(){
  var btn = document.querySelector('button.draw');
  btn.addEventListener('click', function(event){
    var keepGoing = drawAndPlay();
    if( !keepGoing ){
      alert('Game Over');
      clearInterval(window.intervalID);
    }
  });
  
  
  
};
