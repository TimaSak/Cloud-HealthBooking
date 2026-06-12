
<template>
  <div>
    <nav class="navbar navbar-light bg-white shadow-sm mb-4">
      <div class="container d-flex justify-content-between align-items-center">
        <span class="fw-bold fs-5">Doctor Appointments</span>
        <router-link to="/" class="btn btn-outline-primary">⇆ Switch Page</router-link>
      </div>
    </nav>

    <div class="container">
      <div class="card shadow-sm">
        <div class="card-header">
          <h5 class="mb-0">Appointments</h5>
        </div>
        <div class="card-body p-0">
          <table class="table table-bordered table-striped mb-0">
            <thead class="table-light">
              <tr>
                <th>Name</th>
                <th>Symptoms</th>
                <th>Time</th>
                <th>Status</th>
                <th>Update</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="appointment in appointments" :key="appointment.appointmentId">
                <td>{{ appointment.patientName }}</td>
                <td>{{ appointment.symptoms }}</td>
                <td>{{ appointment.slot }}</td>
                <td>{{ appointment.status }}</td>
                <td>
                  <select class="form-select" :value="appointment.status" @change="e => updateStatus(appointment, e.target.value)">
                    <option>Pending</option>
                    <option>In Progress</option>
                    <option>Completed</option>
                  </select>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "BookAppointment",
  data() {
    return {
      name: "",
      symptoms: "",
      selectedSlot: "",
      slots: []
    };
  },
  mounted() {
    // Correct URL for fetching slots
    fetch("https://91y2mgx6o5.execute-api.us-east-1.amazonaws.com/prod/slots")
        .then(res => res.json())
        .then(data => {
          // Bulletproof parsing: Handles both Proxy and Non-Proxy AWS integrations
          const parsed = typeof data === 'string' ? JSON.parse(data) : (data.body ? JSON.parse(data.body) : data);
          this.slots = parsed.filter(s => !s.isBooked).map(s => s.slot);
        })
        .catch(err => console.error("Error fetching slots:", err));
  },
  methods: {
    submitAppointment() {
      const payload = {
        patientName: this.name,
        symptoms: this.symptoms,
        slot: this.selectedSlot
      };

      // UPDATED TO YOUR NEW API URL
      fetch("https://91y2mgx6o5.execute-api.us-east-1.amazonaws.com/prod/appointments", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ body: JSON.stringify(payload) })
      })
          .then(res => res.json())
          .then(() => {
            alert("Appointment booked!");
            this.name = "";
            this.symptoms = "";
            this.selectedSlot = "";
          })
          .catch(err => {
            console.error("Error booking appointment:", err);
            alert("Failed to book appointment.");
          });
    }
  }
};
</script>
