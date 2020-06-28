/*
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

/* global getAssetRegistry getFactory emit */

/**
 * Sample transaction processor function.
 * @param {org.ehr.basic.confirmAppoint} catx The sample transaction instance.
 * @transaction
 */
async function gettingAppointment(catx) {
  if (catx.appoint.status === 'CONFIRMED'){
    throw new Error('Appointment already confirmed');
  }
  if (catx.appoint.status === 'CONSULTED'){
    throw new Error('Appointment already consulted');
  }
  if (catx.appoint.status === 'REJECTED'){
    throw new Error('Appointment already rejected');
  }
  if (catx.doctor.speciality != catx.appoint.group){
    throw new Error('Doctor is not specialized into the required disease category');
  }
  
  catx.appoint.status = 'CONFIRMED';
  catx.appoint.patient = catx.patient;
  catx.appoint.assigned = catx.doctor;
  
  const appointRegistry = await getAssetRegistry('org.ehr.basic.Appointment');
  await appointRegistry.update(catx.appoint);
}

/**
 * Sample transaction processor function.
 * @param {org.ehr.basic.consult} ctx The sample transaction instance.
 * @transaction
 */
async function gettingChecked(ctx) {
  
  ctx.appoint.status = 'CONSULTED';
  ctx.appoint.group = ctx.patient.disease;
  ctx.appoint.consultanceFee = ctx.doctor.consultanceFee;
 // ctx.patient.debt = ctx.patient.debt + ctx.appoint.consultanceFee;
  if ((ctx.appoint.isInsured = true) && (ctx.InsuranceId.ensuredAmount >= ctx.appoint.consultanceFee)){
   	ctx.InsuranceId.ensuredAmount = ctx.InsuranceId.ensuredAmount - ctx.appoint.consultanceFee;
  } 
  else {
   	let test = ctx.appoint.consultanceFee - ctx.InsuranceId.ensuredAmount;
   	ctx.patient.debt = ctx.patient.debt + test;
   	ctx.InsuranceId.ensuredAmount = 0;
   	ctx.appoint.isInsured = false;
  }
    
  
  const appointRegistry = await getAssetRegistry('org.ehr.basic.Appointment');
  await appointRegistry.update(ctx.appoint);
  const patientRegistry = await getParticipantRegistry('org.ehr.basic.Patient');
  await patientRegistry.update(ctx.patient);
  const InsuranceIdRegistry = await getAssetRegistry('org.ehr.basic.Insurance');
  await InsuranceIdRegistry.update(ctx.InsuranceId);
  const prescriptionRegistry = await getAssetRegistry('org.ehr.basic.Prescription');
  await prescriptionRegistry.update(ctx.presc);
}

/**
 * Sample transaction processor function.
 * @param {org.ehr.basic.buyMed} bmtx The sample transaction instance.
 * @transaction
 */
async function gettingMedicine(bmtx) {
  bmtx.patient.debt = bmtx.patient.debt + (bmtx.chemist.costPermg);
  
  const receipt = getFactory().newResource('org.ehr.basic', 'Receipt', 'R_001');
  receipt.providerID = bmtx.chemist.id;
  receipt.providedTo = bmtx.patient.id;
  receipt.amountPaid = bmtx.chemist.costPermg;
  receipt.logTime = bmtx.timestamp;
    
  
  const receiptRegistry = await getAssetRegistry('org.ehr.basic.Receipt');
  await receiptRegistry.addAll([receipt]);
  const patientRegistry = await getParticipantRegistry('org.ehr.basic.Patient');
  await patientRegistry.update(bmtx.patient);
}

/**
 * Sample transaction processor function.
 * @param {org.ehr.basic.buyTest} bttx The sample transaction instance.
 * @transaction
 */
async function gettingTest(bttx) {
  bttx.patient.debt = bttx.patient.debt + bttx.pathlab.cost;
  
  const receipt = getFactory().newResource('org.ehr.basic', 'Receipt', 'R_002');
  receipt.providerID = bttx.pathlab.id;
  receipt.providedTo = bttx.patient.id;
  receipt.amountPaid = bttx.pathlab.cost;
  receipt.logTime = bttx.timestamp;
    
  
  const receiptRegistry = await getAssetRegistry('org.ehr.basic.Receipt');
  await receiptRegistry.addAll([receipt]);
  const patientRegistry = await getParticipantRegistry('org.ehr.basic.Patient');
  await patientRegistry.update(bttx.patient);
}
