
select name,a.user_id from allocations a inner join users u on a.user_id = u.id where a.designation_role='PM';


if you type name,id id is present in both 
so its ambiguous 
so in addition to that you also have to provide id of which table.


![[Pasted image 20251023201440.png]]
