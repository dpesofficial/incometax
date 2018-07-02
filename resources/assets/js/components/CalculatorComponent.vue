<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card card-default">
                    <div class="card-header">Calculate your income tax</div>

                    <div class="card-body">
                        <form>
                            <div class="form-group">
                                <label for="taxFor">Tax For</label>
                                <select v-model="taxForSelected" class="form-control" id="taxFor" aria-describedby="taxForHelp">
                                    <option v-for="(taxForOption, index) in taxForOptions" :value="index">{{ taxForOption }}</option>
                                </select>
                                <small id="taxForHelp" class="form-text text-muted">Select a tax applicable type.</small>
                            </div>
                            <div class="form-group">
                                <label for="monthlySalary">Monthly Salary, NRS</label>
                                <input v-model.number="monthlyIncome" type="text" class="form-control" id="monthlySalary" aria-describedby="monthlySalaryHelp" placeholder="">
                                <small id="monthlySalaryHelp" class="form-text text-muted">Your monthly income in NRS.</small>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
            <div class="col-md-4">
                <div class="card">
                    <div class="card-body">
                        <h3 class="card-title">Result</h3>
                        <h6 class="card-subtitle mb-2 text-muted">The calculated tax amount based on the information you provided.</h6>
                        <p class="card-text">
                            <ul class="list-group">
                                <li class="list-group-item">
                                    <h5><strong>Tax for</strong>: {{ taxForOptions[taxForSelected] }}</h5>
                                </li>
                                <li class="list-group-item">
                                    <h5><strong>Monthly income</strong>: NRS {{ monthlyIncome }}</h5>
                                </li>
                                <li v-if="monthlyIncome > 0" class="list-group-item">
                                    <h5><strong>Annual income</strong>: NRS {{ getAnnualIncome }}</h5>
                                </li>
                                <li v-if="monthlyIncome > 0" class="list-group-item">
                                    <h5><strong>Annual tax amount</strong>: NRS {{ calculateTax }} (NRS {{ monthlyTaxAmount }} monthly)</h5>
                                </li>
                                <li v-if="slabTaxAmounts.length > 0 && monthlyIncome > 0" class="list-group-item">
                                    <h5><strong>Breakdown in slabs</strong></h5>
                                    <ul class="list-unstyled">
                                        <li v-for="(slabTaxAmount, index) in slabTaxAmounts">Slab #{{ index + 1 }} NRS {{ slabTaxAmount.taxableAmount }} - NRS {{ slabTaxAmount.taxAmount }}</li>
                                    </ul>
                                </li>
                            </ul>
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                monthlyIncome: 0,
                totalTax: 0,
                taxForSelected: 0,
                taxForOptions: ["Individual", "Couple"],
                monthlyTaxAmount: 0,
                slabTaxAmounts: []
            }
        },
        mounted() {
            console.log('Component mounted.')
        },
        computed: {
            getAnnualIncome: function () {
                return this.monthlyIncome * 12
            },
            calculateTax: function () {
                if ( ! this.monthlyIncome) return 0;

                this.slabTaxAmounts = []

                let myMonthlySalary = parseFloat(this.monthlyIncome)
                let myAnnualSalary = myMonthlySalary * 12
                let firstSlabTaxableAmount = 350000

                // Couple
                if (1 == this.taxForSelected) {
                    firstSlabTaxableAmount = 400000
                }

                // Containers
                let taxableAmount = 0, taxAmount = 0, totalTaxAmount = 0, remainingSalary = 0

                // 1st slab
                if (myAnnualSalary >= firstSlabTaxableAmount) {
                    taxableAmount = firstSlabTaxableAmount
                    remainingSalary = myAnnualSalary - taxableAmount
                } else {
                    taxableAmount = myAnnualSalary
                    remainingSalary = 0
                }

                // Calculate tax
                if (taxableAmount > 0) {
                    taxAmount = taxableAmount * 0.01
                    totalTaxAmount += taxAmount

                    this.slabTaxAmounts.push({
                        taxableAmount: taxableAmount,
                        taxAmount: taxAmount
                    })
                }

                // 2nd slab
                if (remainingSalary > 0) {
                    if (remainingSalary >= 100000) {
                        taxableAmount = 100000
                        remainingSalary = remainingSalary - taxableAmount
                    } else {
                        taxableAmount = remainingSalary
                        remainingSalary = 0
                    }

                    // Calculate tax
                    taxAmount = taxableAmount * 0.1
                    totalTaxAmount += taxAmount

                    this.slabTaxAmounts.push({
                        taxableAmount: taxableAmount,
                        taxAmount: taxAmount
                    })
                }

                // 3rd slab
                if (remainingSalary > 0) {
                    if (remainingSalary >= 200000) {
                        taxableAmount = 200000
                        remainingSalary = remainingSalary - taxableAmount
                    } else {
                        taxableAmount = remainingSalary
                        remainingSalary = 0
                    }

                    // Calculate tax
                    taxAmount = taxableAmount * 0.2
                    totalTaxAmount += taxAmount

                    this.slabTaxAmounts.push({
                        taxableAmount: taxableAmount,
                        taxAmount: taxAmount
                    })
                }

                // 4th slab
                if (remainingSalary > 0) {
                    if (remainingSalary >= 1350000) {
                        taxableAmount = 1350000
                        remainingSalary = remainingSalary - taxableAmount
                    } else {
                        taxableAmount = remainingSalary
                        remainingSalary = 0
                    }

                    // Calculate tax
                    taxAmount = taxableAmount * 0.3
                    totalTaxAmount += taxAmount

                    this.slabTaxAmounts.push({
                        taxableAmount: taxableAmount,
                        taxAmount: taxAmount
                    })
                }

                // 5th slab
                if (remainingSalary > 0) {
                    taxableAmount = remainingSalary

                    // Calculate tax
                    taxAmount = taxableAmount * 0.36
                    totalTaxAmount += taxAmount

                    this.slabTaxAmounts.push({
                        taxableAmount: taxableAmount,
                        taxAmount: taxAmount
                    })
                }

                this.totalTax = totalTaxAmount.toFixed(2)
                this.monthlyTaxAmount = (this.totalTax / 12).toFixed(2)

                return this.totalTax
            }
        }
    }
</script>
