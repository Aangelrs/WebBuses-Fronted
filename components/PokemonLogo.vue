<template>
    <img class="pokemon-logo" alt="Pokemon Logo" src="/pokedex/PokemonLogo.png" />
</template>
  
  
  <style>
  .pokemon-logo {
    height: 180px;
    width: 100%;
    transform: rotateY(560deg);
    animation: turn 3.5s ease-out forwards 1s;

  }
  
  @keyframes turn {
    100% {
      transform: rotateY(0deg);
    }
  }
  </style>