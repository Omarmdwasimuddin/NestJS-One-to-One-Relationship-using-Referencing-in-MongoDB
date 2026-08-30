## One-to-One Relationship using Referencing in MongoDB


#### Create module, service, controller
```bash
nest g module staff
```
```bash
nest g service staff
```
```bash
nest g controller staff
```
---


>#### folder & file create koro- schemas/profile.schema.ts & schemas/staff.schema.ts

#### `profile.schema.ts`
```bash
import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import { Document } from "mongoose";

@Schema()
export class Profile extends Document {
    @Prop()
    age: number;

    @Prop()
    qualification: string;

    @Prop()
    experience: number;
}

export const ProfileSchema = SchemaFactory.createForClass(Profile);
```

#### `staff.schema.ts`
```bash
import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import { Document, Schema as MongooseSchema } from "mongoose";
import { Profile } from "./profile.schema";

@Schema()
export class Staff extends Document {
    @Prop()
    name: string;

    @Prop({ type: MongooseSchema.Types.ObjectId, ref: 'Profile' })
    profile: Profile
}

export const StaffSchema = SchemaFactory.createForClass(Staff);
```
---
