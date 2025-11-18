
![](https://www.youtube.com/watch?v=DnRY5yG67u8)


![[Pasted image 20251111223451.png]]


so the problem of props drilling is solved 
one ways is to use useContext hook.


Another way to overcome is to redux store ,

![[Pasted image 20251111223929.png]]



![[Pasted image 20251111224748.png]]   




![[Pasted image 20251111225010.png]]   



 ![[Pasted image 20251111225034.png]]

![[Pasted image 20251111225657.png]]


![[Pasted image 20251111233819.png]]
![[Pasted image 20251111233843.png]]



now lets use it in code 

so inside source folder,
create a redux folder

first we need to create a store so lets copy the code from configureStore

![[Pasted image 20251112015330.png]]


![[Pasted image 20251112015405.png]]  

some reducers are already there, so we need to remove them 

![[Pasted image 20251112015504.png]]  

so this is our starter template code 


step 1)  create a config store 
step 2)  wrap our component in redux provider 
step 3) creating a slice 
step 4) register all the reducers in the store 

for wrapping we need two components,


import provider and store and wrap it into our component

![[Pasted image 20251112015800.png]]  

inside that store we will place our entire logic ,

why wrapping  with store and provider
so that the store component which i created to be accessible through out our app component,
so thats why wrapping it.




![[Pasted image 20251112021118.png]]

so now i have created a features folder, 
inside that i have created a counter folder ,

inside that i have  created counterSlice.jsx 
now lets put the code into it 


![[Pasted image 20251112021326.png]]   

what i had already mentioned that in slice we have a initial state and reducers functions,

which is the same we can see here......


these increment,decrement,incrementByamount are actions ;

but our store  file is still empty , 

so we need to register these functions in our  storr file 

only if the store has the counter reducers stored in it, then only it can use them 




so now registered our configureStore 


so now as we have already defined the code for  increment,decrement

now lets add the ui logic to it , 

![[Pasted image 20251112023618.png]]    


![[Pasted image 20251112023722.png]]    

this is the basic code added 

![[Pasted image 20251112023948.png]]   

so whenever we have to fetch values, we need to always use useSelector hook,
state ke andar counter naam ka slice , uske andar value ka use karna hai hamein 
and 
whenever i need to execute any actions, i need to use 
dispatch hook to make those actions happen 



![[Pasted image 20251112025254.png]]


so this is the react redux toolkit 

