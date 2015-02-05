//1 Find all pizzas eaten by at least one female over the age of 20. 
\project_{pizza} (
    (\select_{age > '20' and gender='female'} Person)
    \join Eats
);

//2 Find the names of all females who eat at least one pizza served by Straw Hat. (Note: The pizza need not be eaten at Straw Hat.) 
\project_{name} (
    \project_{pizza}
        (\select_{pizzeria = 'Straw Hat'} Serves)
    \join Eats // ate pizza
    \join (\select_{gender = 'female'} Person)
);

//3 Find all pizzerias that serve at least one pizza for less than $10 that either Amy or Fay (or both) eat.
\project_{pizzeria} (
    \project_{pizza}
        (\select_{name = 'Amy' or name = 'Fay'} Eats)
    \join (\select_{price < '10'} Serves)
);

//4 Find all pizzerias that serve at least one pizza for less than $10 that both Amy and Fay eat. 
\project_{pizzeria} (
    \project_{pizza}
        (\select_{name = 'Amy'} Eats)
    \intersect
    \project_{pizza}
        (\select_{name = 'Fay'} Eats)
   
    \join (\select_{price < '10'} Serves)
);

//5 Find the names of all people who eat at least one pizza served by Dominos but who do not frequent Dominos. 
\project_{name} (
    (\select_{pizzeria = 'Dominos'} Serves) 
    \join Eats
)
\diff
\project_{name} (
    \select_{pizzeria = 'Dominos'} Frequents
)

//6 Find all pizzas that are eaten only by people younger than 24, or that cost less than $10 everywhere they're served. 
(\project_{pizza} Serves
\diff
\project_{pizza}
    (\select_{price >= 10} Serves))
    
\union

(\project_{pizza} Eats
\diff
\project_{pizza}
    (\select_{age >= 24} Person 
     \join Eats)
)

//7 Find the age of the oldest person (or people) who eat mushroom pizza. 
\rename_{a1}(
    \project_{age} (
        \project_{name} (
            \select_{pizza='mushroom'} Eats) \join Person))
\diff
\project_{a1}(\rename_{a1}(
    \project_{age} (
        \project_{name} (
            \select_{pizza='mushroom'} Eats) \join Person))
\join_{a2>a1} // \join_{a2 < a1} would give the youngest person
\rename_{a2}(
    \project_{age} (
        \project_{name} (
            \select_{pizza='mushroom'} Eats) \join Person))
)


//8 Find all pizzerias that serve only pizzas eaten by people over 30.
\project_{pizzeria} Serves
\diff
\project_{pizzeria} (
    Serves
    \join
    ((\project_{pizza} Serves)
        \diff
       (\project_{pizza}((
            \select_{age > '30'} Person) \join Eats)))
)

//9 Find all pizzerias that serve every pizza eaten by people over 30. (Division)
\project_{pizzeria} Serves
\diff
(\project_{pizzeria}( 
	(\project_{pizzeria} Serves) //all pizzeria
	//all pizzeria and pizza eaten by >30 cartesian products
	\cross // \join
	(\project_{pizza}( //all pizza eaten by >30
		\select_{age > '30'} Person \join Eats))
	\diff
	(\project_{pizzeria, pizza}( //pizzeria that serves pizza eaten by >30
		\select_{age > '30'} Person \join Eats \join Serves))	
	)
)
