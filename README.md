# Global-Relic-Grader-HSR-
An updated version of my initial relic grader based on the Fribbels scoring substat scoring algorithm (https://fribbels.github.io/hsr-optimizer#scorer)

Weights are the general amount that a specified unit likes a certian substat scaled from 0 to 1.
for example if a character performs well with a high attack stat but changes to the defense stat are seen to be negligable those weights may look like the following in my database:

Test_weights: weight = { 
    "ATK": 0.75,
    "DEF": 0,
}

The previous was just an example but in practice there are 9 total weighted stats in each character's weight dictionary. 

For the rest of my examples I'll use the following character weight dictionary:

---------------------------------

Tingyun_weights: weight = {
    "HP": 0.75, 
    "FLAT_HP": 0.75,
    "ATK": 1,
    "FLAT_ATK": 0,
    "DEF": 0.75,
    "FLAT_DEF": 0.75,
    "SPD": 1,
    "CR": 0,
    "CD": 0,
    "EHR": 0,
    "RES": 0.75,
    "BE": 0,
}


---------------------------------

Although Tingyun in this example likes SPD (speed) and ATK (attack) the same amount, in her performance she might not value 10 speed the same way she values 10 attack. This is because both statlines opperate on two completely different ratios, just looking at Tingyun's base stats we see that their minum ratios have a difference of over 6 times. And looking at simulations of calculated gamplay it's been unanamisly agreed upon that in a majority of cases (including Tingyuns's) that the value of the speed stat has diminishing returns after having a speed value of over 160 SPD, and the value of the attack stat at around 2600 ATK. And while both statlines are equally valuable you can see they opperate at two very different Ratios.

---------------------------------

Tingyun_base_stats: base_statline = {
    "HP": 846,
    "ATK": 529,
    "DEF": 396,
    "SPD": 112,
    "taunt": 100
}

---------------------------------


Endgamestat stat expectation list after equiping relics with prefered stats:

---------------------------------

HP: 3000 - 3500+

DEF: 900 - 1100+

ATK: 2400 - 2600+

SPD: 134 / 143 (Recommended) / 160+


---------------------------------

We compensate for these differences in ratio's by multiplying each value by a normalization constant for each stat when calculated the score. These values are from the and the formulas preceeding them are from the fribbels.github.io relic scorer 

---------------------------------

normalizations = {
    "HP": 1.5, 
    "ATK": 1.5,
    "DEF": 1.2,
    "SPD": 2.59,
    "CR": 2,
    "CD": 1,
    "EHR": 1.5,
    "RES": 1.5,
    "BE": 1,
}

---------------------------------

So the total score from each substat looks like this:

score = weight * normalization * stat_value

---------------------------------

Everything here would be finished if every stat came in the form of just a pre-determined value. But instead in the cases of the stats DEF, HP, and ATK they can come in two forms. One form is the aformentioned set value Ex. 'HP': 42. The other form is in a percentage value Ex. 8.2%. This percentage is of the character's base stat + the stat their lightcone gives them. If you don't play Honkai Star Rail a light cone *roughly* doubles a base stat, so we're going to call this 8.2% of character base * 2. Funnily enough since these are the only stats that can appear as flat values, its easier if we convert the flat values to percentage values with this calculation by Fribbles.

1 / (2 * character base * 0.01) * (64.8 / (% main stat value))
"This converts the flat stat value to a percent equivalent by base stats, then normalizes it."

That calculation gives us a new normalization value to use that lets us use the stat value at a more acurate ratio

score = weight * normalization * stat_value

---------------------------------  

Lastly since there are four substats in total we repeat this on every substat and add all four scores together to find the total score of all substats for a given piece
