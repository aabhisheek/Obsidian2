
```javascript
import { z } from "zod";

  

import { createPaginatedSchema, defaultMeta } from "@/features/schema";

import {

  userRequestStatusTypeArray,

  userRequestTypeArray,

} from "@/features/user-request/constants";

import { userSchema } from "@/features/users/schema";

//enums...........  

const userRequestTypeEnumArray = userRequestTypeArray.map(

  (item) => item.value

) as [(typeof userRequestTypeArray)[number]["value"]];

export const userRequestTypeEnum = z.enum(userRequestTypeEnumArray);

export type UserRequestTypeEnum = z.infer<typeof userRequestTypeEnum>;

  

export const userRequestStatusTypeEnum = z.enum(userRequestStatusTypeArray);

export type UserRequestStatusTypeEnum = z.infer<

  typeof userRequestStatusTypeEnum

>;

  ///////////////base schemaaaa

export const userRequestSchema = z.object({

  createdAt: z.string(),

  updatedAt: z.string(),

  id: z.number(),

  empEmail: z.string(),

  requestFromDate: z.date().or(z.string()),

  requestToDate: z.date().or(z.string()),

  requestType: userRequestTypeEnum,

  comments: z.string().min(1, { message: "Comments cannot be empty" }),

  status: userRequestStatusTypeEnum,

  statusUpdatedBy: z.string().nullable().optional(),

  user: userSchema,

  isHalfDay: z.boolean().optional().nullable(),

  selectManager: z.string().optional(),

});

export type UserRequestSchema = z.infer<typeof userRequestSchema>;

/////  comp_off subschemaaaa  

const statusHistorySchema = z.object({

  action: z.string(),

  actionerName: z.string(),

  projectName: z.string(),

  role: z.string(),

});

  

const projectApprovalStatusSchema = z.object({

  projectName: z.string(),

  statusHistory: z.array(statusHistorySchema),

});

  

export const userRequestStatusHistory = userRequestSchema.extend({

  hrAction: userRequestStatusTypeEnum,

  totalProjects: z.number(),

  projectApprovalStatuses: z.array(projectApprovalStatusSchema),

  referenceFileUrl: z.string().optional().nullable(),

});

  

export type UserRequestStatusHistory = z.infer<typeof userRequestStatusHistory>;

  

export const userRequestArraySchema = z.array(userRequestStatusHistory);

export type UserRequestArraySchema = z.infer<typeof userRequestArraySchema>;

  

export const getUserRequestUsingEmailSchema = z.object({

  fromDate: z.date().or(z.string()),

  toDate: z.date().or(z.string()),

  requestType: userRequestTypeEnum,

  employeeEmail: z.string().email(),

});

export type GetUserRequestUsingEmailSchema = z.infer<

  typeof getUserRequestUsingEmailSchema

>;

  

export const getUserRequestSchema = getUserRequestUsingEmailSchema.omit({

  employeeEmail: true,

});

export type GetUserRequestSchema = z.infer<typeof getUserRequestSchema>;

  

export const getUserRequestUsingDateSchema = z.object({

  date: z.date().or(z.string()),

  requestType: userRequestTypeEnum,

});

export type GetUserRequestUsingDateSchema = z.infer<

  typeof getUserRequestUsingDateSchema

>;

  

export const CompOffSchema = z.object({

  managers: z.string().min(1, "Manager is required"),

  isHalfDay: z.boolean().default(false),

  // date: z.date(), --> This is already passed

  // hoursWorked: z.number(), --> Not required

  // description: z.string().optional(), --> Replace with the comment

});

  

export type CompOffSchemaType = z.infer<typeof CompOffSchema>;

  

export const ManagerSchema = z.object({

  id: z.number(),

  name: z.string(),

  email: z.string().email(),

});

  

export type ManagerSchemaType = z.infer<typeof ManagerSchema>;

  

export const clientProjectStatusSchema = z.object({

  message: z.string(),

  data: z.object({

    status: z.boolean(),

  }),

});

export type ClientProjectStatusSchema = z.infer<

  typeof clientProjectStatusSchema

>;

  

export const createUserRequestSchema = userRequestSchema

  .pick({

    requestFromDate: true,

    requestToDate: true,

    comments: true,

  })

  .extend({

    userRequestType: userRequestTypeEnum,

    compOffDetails: z.array(CompOffSchema).optional(),

    clientApproval: z.boolean().optional(),

    file: z.instanceof(File).optional(),

    isHalfDay: z.boolean().optional(),

  });

export type CreateUserRequestSchema = z.infer<typeof createUserRequestSchema>;

  

export const updateUserRequestSchema = createUserRequestSchema

  .pick({

    comments: true,

    requestFromDate: true,

    requestToDate: true,

    clientApproval: true,

    file: true,

    isHalfDay: true,

  })

  .extend({

    userRequestId: z.number(),

    message: z.string().optional(),

  });

export type UpdateUserRequestSchema = z.infer<typeof updateUserRequestSchema>;

  

export const deleteUserRequestSchema = z.object({

  userRequestId: z.number(),

});

export type DeleteUserRequestSchema = z.infer<typeof deleteUserRequestSchema>;

  

const metaFields = defaultMeta.extend({

  fromDate: z.date().or(z.string()),

  toDate: z.date().or(z.string()),

  leaves: z.number(),

});

  

export const paginatedUserRequestSchema = createPaginatedSchema(

  "userRequests",

  userRequestStatusHistory,

  metaFields

);

export type PaginatedUserRequestSchema = z.infer<

  typeof paginatedUserRequestSchema

>;
```



updateSuggested by gpt

```javascript
import { z } from "zod";
import { createPaginatedSchema, defaultMeta } from "@/features/schema";
import {
  userRequestStatusTypeArray,
  userRequestTypeArray,
} from "@/features/user-request/constants";
import { userSchema } from "@/features/users/schema";

/* ---------------- ENUMS ---------------- */

const userRequestTypeEnumArray = userRequestTypeArray.map(
  (item) => item.value
) as [(typeof userRequestTypeArray)[number]["value"]];

export const userRequestTypeEnum = z.enum(userRequestTypeEnumArray);
export type UserRequestTypeEnum = z.infer<typeof userRequestTypeEnum>;

export const userRequestStatusTypeEnum = z.enum(userRequestStatusTypeArray);
export type UserRequestStatusTypeEnum = z.infer<typeof userRequestStatusTypeEnum>;

/* ---------------- BASE SCHEMA ---------------- */

export const userRequestSchema = z.object({
  createdAt: z.string(),
  updatedAt: z.string(),
  id: z.number(),
  empEmail: z.string(),
  requestFromDate: z.date().or(z.string()),
  requestToDate: z.date().or(z.string()),
  requestType: userRequestTypeEnum,
  comments: z.string().min(1, { message: "Comments cannot be empty" }),
  status: userRequestStatusTypeEnum,
  statusUpdatedBy: z.string().nullable().optional(),
  user: userSchema,
  isHalfDay: z.boolean().optional().nullable(),
  selectManager: z.string().optional(),
});

export type UserRequestSchema = z.infer<typeof userRequestSchema>;

/* ---------------- COMPOFF SUBSCHEMA ---------------- */

const CompOffSchema = z.object({
  managers: z.string().optional(), // 👈 manager now optional here
  isHalfDay: z.boolean().default(false),
});

/* ---------------- CREATE REQUEST SCHEMA ---------------- */

export const createUserRequestSchema = z
  .object({
    requestFromDate: z.date(),
    requestToDate: z.date(),
    comments: z.string().min(1, "Comments cannot be empty"),
    userRequestType: userRequestTypeEnum,
    compOffDetails: z.array(CompOffSchema).optional(),
    clientApproval: z.boolean().optional(),
    file: z.instanceof(File).optional(),
    isHalfDay: z.boolean().optional(),
  })
  .superRefine((data, ctx) => {
    // ✅ Enforce manager only for COMP_OFF requests
    if (data.userRequestType === "COMP_OFF") {
      const manager = data.compOffDetails?.[0]?.managers;
      if (!manager || manager.trim().length === 0) {
        ctx.addIssue({
          path: ["compOffDetails", 0, "managers"],
          code: z.ZodIssueCode.custom,
          message: "Manager is required for Comp Off",
        });
      }
    }
  });

export type CreateUserRequestSchema = z.infer<typeof createUserRequestSchema>;

```