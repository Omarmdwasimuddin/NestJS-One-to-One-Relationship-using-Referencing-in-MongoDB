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



#### `staff.module.ts`
```bash
import { Module } from '@nestjs/common';
import { StaffService } from './staff.service';
import { StaffController } from './staff.controller';
import { MongooseModule } from '@nestjs/mongoose';
import { Staff, StaffSchema } from './schemas/staff.schema';
import { Profile, ProfileSchema } from './schemas/profile.schema';

@Module({
  imports: [
    MongooseModule.forFeature([
      { name: Staff.name, schema: StaffSchema },
      { name: Profile.name, schema: ProfileSchema }
    ]),
  ],
  providers: [StaffService],
  controllers: [StaffController]
})
export class StaffModule {}
```
---



#### `staff.service.ts`
```bash
import { Injectable } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Staff } from './schemas/staff.schema';
import { Model } from 'mongoose';
import { Profile } from './schemas/profile.schema';

@Injectable()
export class StaffService {
  constructor(
    @InjectModel(Staff.name) private staffModel: Model<Staff>,
    @InjectModel(Profile.name) private profileModel: Model<Profile>,
  ) {}

  async createStaff(): Promise<Staff> {

    const profile = await new this.profileModel({
      age: 30,
      qualification: "MBA",
      experience: 5,
    }).save();

    const staff = new this.staffModel({
      name: "John Doe",
      profile: profile._id,
    });

    return staff.save();
  }

  async findAll(): Promise<Staff[]> {
    return this.staffModel.find().populate('profile').exec();
  }
}
```
---
