<template>
    <div class="about">
        <div class="d-flex flex-column justify-content-center align-items-center">
            <h1 class="mt-4">Agenda de Contactos</h1>
        </div>

        <div>
            <div class="row">
                <div class="col-md-6">
                    <div class="mb-3">
                        <div class="input-group">
                            <span class="input-group-text" id="description-search-text"><i class="bi bi-search"></i>
                                Buscar por
                                Nombre </span>
                            <input type="search" class="form-control" id="description-search"
                                @search="this.toSearch = $event.target.value">
                        </div>
                    </div>
                </div>
                <div class="col-md-6 d-flex justify-content-end">
                    <div class="mb-3">
                        <button type="button" class="btn btn-success btn-sm" @click="createNew()">
                            <i class="bi bi-file-plus"></i> Agregar nuevo
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div class="card">
            <div class="card-body">
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th scope="col">#</th>
                            <th scope="col">Id</th>
                            <th scope="col">Name</th>
                            <th scope="col">Email</th>
                            <th scope="col">Address</th>
                            <th scope="col">Phone</th>
                            <th scope="col">Country</th>
                            <th scope="col">City</th>
                            <th></th>
                        </tr>
                    </thead>

                    <tbody>
                        <tr v-for="(agenda, index) in getList()" :key="index">
                            <th scope="row">{{ 1 + index }}</th>
                            <td>{{ agenda.id }}</td>
                            <td>{{ agenda.name }}</td>
                            <td>{{ agenda.email }}</td>
                            <td>{{ agenda.address }}</td>
                            <td>{{ agenda.phone }}</td>
                            <td>{{ agenda.country }}</td>
                            <td>{{ agenda.city }}</td>
                            <td>
                                <button type="button" class="btn btn-info btn-sm" @click="edit(index)"><i
                                        class="bi bi-pen"></i></button>

                                <button type="button" class="btn btn-danger btn-sm" @click="remove(index)"><i
                                        class="bi bi-trash3"></i></button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <div class="modal fade" id="createModal" tabindex="-1" aria-labelledby="createModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="createModalLabel">Crear Nuevo</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <form @submit.prevent="save()">
                        <div class="modal-body">
                            <div class="row">
                                <div class="col-md-6">
                                    <div class="mb-3">
                                        <label for="name" class="form-label">Nombre</label>
                                        <input type="text" class="form-control" id="name" v-model="newAgenda.name">
                                    </div>
                                    <div class="mb-3">
                                        <label for="email" class="form-label">Correo</label>
                                        <input type="text" class="form-control" id="email" v-model="newAgenda.email">
                                    </div>
                                    <div class="mb-3">
                                        <label for="phone" class="form-label">Telefono</label>
                                        <input type="text" class="form-control" id="phone" v-model="newAgenda.phone">
                                    </div>
                                </div>
                                <div class="col-md-6">
                                    <div class="mb-3">
                                        <label for="createCountry" class="form-label">Pais</label>
                                        <select class="form-select" v-model="newAgenda.country" id="createCountry">
                                            <option v-for="country in countrys" :value="country" :key="country">
                                                {{ country }}</option>
                                        </select>
                                    </div>
                                    <div class="mb-3">
                                        <label for="createCity" class="form-label">Ciudad</label>
                                        <select class="form-select" v-model="newAgenda.city" id="createCity">
                                            <option v-for="city in citys" :value="city" :key="city">{{ city }}</option>
                                        </select>
                                    </div>
                                    <div class="mb-3">
                                        <label for="address" class="form-label">Direccion</label>
                                        <input type="text" class="form-control" id="address"
                                            v-model="newAgenda.address">
                                    </div>
                                </div>
                            </div>

                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                            <button type="submit" class="btn btn-primary">Guardar cambios</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <!-- Modal Edit -->
        <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="editModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="editModalLabel">Editar</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <form @submit.prevent="saveEdit()">
                        <div class="modal-body" v-if="itemSelected">
                            <div class="row">
                                <div class="col-md-6">
                                    <div class="mb-3">
                                        <label for="updatename" class="form-label">Nombre</label>
                                        <input type="text" class="form-control" id="updatename"
                                            v-model="itemSelected.name">
                                    </div>
                                    <div class="mb-3">
                                        <label for="updateemail" class="form-label">Correo</label>
                                        <input type="text" class="form-control" id="updateemail"
                                            v-model="itemSelected.email">
                                    </div>
                                    <div class="mb-3">
                                        <label for="updatephone" class="form-label">Telefono</label>
                                        <input type="text" class="form-control" id="updatephone"
                                            v-model="itemSelected.phone">
                                    </div>
                                </div>
                                <div class="col-md-6">

                                    <div class="mb-3">
                                        <label for="updateCountry" class="form-label">Pais</label>
                                        <select class="form-select" v-model="itemSelected.country" id="updateCountry">
                                            <option v-for="country in countrys" :value="country">{{ country }}</option>
                                        </select>
                                    </div>
                                    <div class="mb-3">
                                        <label for="updateCity" class="form-label">Ciudad</label>
                                        <select class="form-select" v-model="itemSelected.city" id="updateCity">
                                            <option v-for="city in citys" :value="city">{{ city }}</option>
                                        </select>
                                    </div>
                                    <div class="mb-3">
                                        <label for="updateaddress" class="form-label">Direccion</label>
                                        <input type="text" class="form-control" id="updateaddress"
                                            v-model="itemSelected.address">
                                    </div>
                                </div>
                            </div>

                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                            <button type="submit" class="btn btn-primary">Guardar cambios</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
export default {
    data() {
        return {
            countrys: ['Argentina', 'Bolivia', 'Colombia', 'Francia'],
            citys: ['Buenos aires', 'Beni', 'Medellin', 'Paris'],
            newAgenda: {
                id: 0,
                name: '',
                email: '',
                address: '',
                phone: '',
                country: '',
                city: ''
            },
            agendasArray: [
                {
                    id: 1,
                    name: "Alice Johnson",
                    email: "alice.johnson@example.com",
                    address: "123 Maple Street",
                    phone: "123-456-7890",
                    country: "Argentina",
                    city: "Buenos aires"
                },
                {
                    id: 2,
                    name: "Jose Luis Pinaya",
                    email: "jose.pinaya@example.com",
                    address: "456 Oak Avenue",
                    phone: "987-654-3210",
                    country: "Bolivia",
                    city: "Beni"
                },
                {
                    id: 3,
                    name: "Antonio Salas",
                    email: "antonio.sls@example.com",
                    address: "456 Oak Avenue",
                    phone: "987-654-3210",
                    country: "Colombia",
                    city: "Medellin"
                },
                {
                    id: 4,
                    name: "Bob Smith",
                    email: "bob.smith@example.com",
                    address: "456 Oak Avenue",
                    phone: "987-654-3210",
                    country: "Francia",
                    city: "Paris"
                }
            ],
            itemSelected: null,
            indexSelected: null,
            typeFilter: '',
            toFilter: '',
            toSearch: ''
        }
    },
    methods: {
        createNew() {
            const createModal = new bootstrap.Modal(document.getElementById('createModal'));
            createModal.show();
            this.newAgenda.id = 0;
            this.newAgenda.name = '';
            this.newAgenda.email = '';
            this.newAgenda.address = '';
            this.newAgenda.phone = '';
            this.newAgenda.country = '';
            this.newAgenda.city = '';
        },
        save() {
            // Validar que los campos no estén vacíos
            if (this.newAgenda.name && this.newAgenda.email && this.newAgenda.address && this.newAgenda.phone && this.newAgenda.country && this.newAgenda.city) {
                const lastId = this.agendasArray.length > 0 ? this.agendasArray[this.agendasArray.length - 1].id : 0;
                this.newAgenda.id = lastId + 1;
                // Agregar un nuevo objeto al array
                this.agendasArray.push({ ...this.newAgenda });
                // Limpiar los campos del formulario
                this.newAgenda.id = 0;
                this.newAgenda.name = '';
                this.newAgenda.email = '';
                this.newAgenda.address = '';
                this.newAgenda.phone = '';
                this.newAgenda.country = '';
                this.newAgenda.city = '';

                // Cerrar el modal
                //const createModal = new bootstrap.Modal(document.getElementById('createModal'));
                const modal = document.getElementById('createModal'); // Asegúrate de usar el ID de tu modal
                const modalInstance = bootstrap.Modal.getInstance(modal);
                modalInstance.hide();

            } else {
                alert("Por favor, completa todos los campos.");
            }
        },
        remove(index) {
            if (confirm("¿Está seguro de eliminar este contacto?")) {
                this.agendasArray.splice(index, 1);
            }
        },
        edit(index) {
            this.itemSelected = { ...this.agendasArray[index] };
            this.indexSelected = index;
            const editModal = new bootstrap.Modal(document.getElementById('editModal'));
            editModal.show();
        },
        saveEdit() {
            this.agendasArray[this.indexSelected] = { ...this.itemSelected };

            const modalElement = document.getElementById('editModal');
            const modalInstances = bootstrap.Modal.getInstance(modalElement) || new bootstrap.Modal(modalElement);
            modalInstances.hide();

            this.itemSelected = null;
            this.indexSelected = null;
        },
        getList() {
            let result = this.agendasArray.filter((item) => {
                if (this.toSearch) {
                    return item.name.toLowerCase().includes(this.toSearch.toLowerCase());
                    //return item.name.includes(this.toSearch);
                }
                return true;
            });
            return result;
            // return this.linksArray;
        }

    }
}
</script>

<style>
.btn {
    margin-right: 3px;
}

.card {
    overflow-x: auto;
}
</style>