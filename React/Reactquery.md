topicsToStudy in Reactquery
api fetch
error handling
success
post pe revalidate
we replace useEffect  to react query because of many errors in react query   


topicsTostudyForReact
Zustand
Mobiquix
Redux
Functional components




so as developers we know,

there is no standard way of making an api call on React

So react query kind of standardises the way of making api calls in  react.

so its gives somes features which are missing in react.



1)Client-side cacheing
2)error-handling
3)re-fetching the data
4)cacheing
5)pagination

how you can use mutations,fetching data in react-query

in version3, 
react query was a standalone package.

in version4,
there came a different package tanStack query in which react query is a Package.

so thats how react-query became  query.

download the folder 
then run npm i in client and server 

then do npm i @tanstack/react-query

in client 

1)C:\Users\anand\Downloads\piyushGarg\Complete Code-20251107T095032Z-1-001
in this folders, we have two codes   starterCode and CompleteCode.

in the above location.

now the server code contains a dummy express Server, in which i can make a few api calls , fetch data and mutate data.

so we will use this server to understand react-query

so lets move to the client 









then 

1) We have to wrap our whole app into our query client provider 
![[Pasted image 20251107161353.png]]

then we have a client into it, queryClient
so lets first define  a queryClient object of queryClient class ,
which has all the methods for fetching,refetching the data.


now wrap your application inside queryClientProvider mentioning the specific client you will use

now our react application is ready to use react query.



2) now lets make some fake api calls to be able to use Reactquery 
so we have 3 routes

a)get to get todos
b)post to create a new todo
c)and a post request to mark the todo as complete


now first lets fetch;
to fetch the data there is a hook in react query called useQuery 
which i will use 

useQuery is hook which i can use to fetch data from my apicalls,

![[Pasted image 20251111160302.png]]


there are two parameters in useQuery,
first is the list of dependencies ,
second one is the callback function associated with the getCall in which i will make my api call.


then there is this key todo  associated with data
so if i want to  refetch the data/refresh the data i can do that with the help of this key.

![[Pasted image 20251111161251.png]]


![[Pasted image 20251111161320.png]]

![[Pasted image 20251111161438.png]]

![[Pasted image 20251111161454.png]]


![[Pasted image 20251111162444.png]]

the data fetched in api is stored in data parameter;



now after this in app.jsx

now i will create a basic form 
and perform data mutation on it

mutations means when we use react Query to create/update   the data.


also we will see how using this key todo we will also refetch the data.

so now i will create a newfolder Components , and inside that i will create forms.jsx



so now creating Form 
![[Pasted image 20251111171303.png]]


![[Pasted image 20251111171402.png]]


now to use Mutations we have useMutate hooks,

![[Pasted image 20251111171854.png]]


next arguments are some callBack functions ;
in useMutations


![[Pasted image 20251111172026.png]]

so this is how you make a mutation;
now lets playaround with it in our forms and see the behaviour 


![[Pasted image 20251111172537.png]]


![[Pasted image 20251111172703.png]]


![[Pasted image 20251111172733.png]]



![[Pasted image 20251111172859.png]]


change text to title 

JUST LEARN how to input data through 1 form and mutate it 
rest we have to just iterate it 

same for zod validation 

same for redux stateManagement

![[Pasted image 20251111173225.png]]


so now after changing to title, lets see in the consoleLog  what to do

so now if we see we have 2 todos 
![[Pasted image 20251111173328.png]]

![[Pasted image 20251111173405.png]]![[Pasted image 20251111173405.png]]


![[Pasted image 20251111173447.png]]
expanding this i get a title here


but till now we have not used the key to perform any meaningful operations

so lets first render these todos

![[Pasted image 20251111173814.png]]

to

![[Pasted image 20251111173851.png]]


![[Pasted image 20251111173912.png]]


so when i click create 

first useMutation will be called,
then toMutation.mutate() will run,

![[Pasted image 20251111174516.png]]


but still i cannot see my todolist rendered 

when i hit refresh, then only i am able to see it 

to solve this problem we have this concept of key which here is toDo.

the change in behavour i want here , when i do mutation, todo mutation 

and it is successfully mutated, 

then i want to refetch all the todos

so we have something like queryClient, so we will fetch this queryClient 

![[Pasted image 20251111180653.png]]  

import it and create  a object of it here 

so just add this line of   invalidatequeries 

USE   invalidatequeries to get past this problem of refreshing only,
data showing 

![[Pasted image 20251111180814.png]]

todo is just name of that Hook which will be rerun and will rerender the data ;

now its automatically being fetched 



now in order to use Devtools 
simply first install tanstackquery/devtools , now you can import it since that file is added 

now   Simply do this ,
and you will be able to do it 

![[Pasted image 20251111182306.png]]

reactdevtools is only this that  i can refresh,invalidate,reset and remove from
devtools only 
![[Pasted image 20251111184131.png]]




now we have certain more parameters to it 

![[Pasted image 20251111184411.png]]  

besides data...

![[Pasted image 20251111184637.png]]


![[Pasted image 20251111184559.png]]




this tutorial basically explains to you the   installation and quick start documentation of   tanStack query 

this and other blogs are provided by,the author of these blogs is the main
I'm a Web Developer and open source maintainer who ❤️ ReactJs and TypeScript. I'm currently maintaining [TanStack/query](https://github.com/TanStack/query) and [TanStack/router](https://github.com/TanStack/router).

for further reads, you can check 
![](https://tkdodo.eu/blog/practical-react-query#keep-server-and-client-state-separate)         and tanstack query official Documentations 



also tanStack query Documentation is very concise and crisp.


