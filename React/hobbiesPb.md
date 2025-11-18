





1)hobbies: z.array(z.object({ value: z.string() })).or(z.array(z.string())),


2)const hobbiesArray = useDynamicFieldArray(control, "hobbies");

  const languagesArray = useDynamicFieldArray(control, "languagesKnown");

3)

 ```javascript
      {/* Dynamic Hobbies */}

      <div className="mb-4">

        <label className="font-semibold">Hobbies</label>

        <div className="flex flex-row flex-wrap gap-2 mt-2">

          {hobbiesArray.fields.map((field, idx) => (

            <div key={field.id} className="flex items-center gap-1">

              <FormInput

                control={control}

                name={`hobbies.${idx}.value`}

                placeholder="e.g., Reading"

              />

              {idx > 0 && (

                <Button

                  type="button"

                  variant="destructive"

                  size="sm"

                  onClick={() => hobbiesArray.remove(idx)}

                >

                  <X className="w-4 h-4" />

                </Button>

              )}

            </div>

          ))}

          <Button

            type="button"

            variant="secondary"

            size="sm"

            className="h-9"

            onClick={() => hobbiesArray.append({ value: "" })}

          >

            <Plus className="w-4 h-4" /> Add

          </Button>

        </div>

      </div>
   ```





4)   ```javascript
   const { passportPhoto, resume, relievingLetter, payslips, ...otherValues } =

      values;

    const userData = {

      ...otherValues,

      dob: format(new Date(otherValues.dob), "dd-MM-yyyy"),

      hobbies: otherValues.hobbies.map(extractValue),

      languagesKnown: otherValues.languagesKnown.map(extractValue),

      techSkills: {

        ...otherValues.techSkills,
   ```

this is the payload when i submit form through  frontend 

"hobbies":["reading","dancing","skating"]


```text
{"name":"ABHISHEK ANAND","phoneNumber":"9650734824","personalEmail":"anand.abhishek9650734826@gmail.com","dob":"05-11-2025","gender":"male","yoe":0,"address":{"city":"PATNA","state":"Bihar","country":"India","address":"PATLIPUTRA"},"techSkills":{"primarySkills":["javascript","node",""],"techSkills":[{"name":"nodejs","rating":1},{"name":"react","rating":1}]},"hobbies":["reading","dancing","skating"],"bloodGroup":"A+","languagesKnown":["English","hindi",""],"tshirtSize":"3","emergencyContact":[{"name":"Abhishek","phoneNumber":"9650734825","relation":"Friend"}],"education":[{"degree":"Btech","university":"KIIT","yearOfPassing":"2024","score":"8"}],"experience":[],"freelancerData":{"weeklyAvalibility":"","preferredHours":""}}
```