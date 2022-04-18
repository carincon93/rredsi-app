<script>
    import AuthenticatedLayout, { title } from '@/Layouts/Authenticated'
    import { inertia, useForm, page } from '@inertiajs/inertia-svelte'
    import { route, checkRole, checkPermission } from '@/Utils'
    import { _ } from 'svelte-i18n'

    import Form from './Form'
    import FormEmpresario from './FormEmpresario'
    import InfoMessage from '@/Shared/InfoMessage'
    import Button from '@/Shared/Button'

    export let errors
    export let institucionEducativa
    export let institucionesEducativas
    export let roles
    export let semilleroInvestigacion

    let tiposDocumento = [
        { value: 'cc', label: 'Cédula de ciudadanía' },
        { value: 'ti', label: 'Tarjeta de identidad' },
        { value: 'ce', label: 'Cédula de extranjería' },
    ]

    let tipoUsuario = 'rredsi'

    $: $title = 'Crear usuario'

    /**
     * Permisos
     */
    let authUser = $page.props.auth.user
    let isSuperAdmin = checkRole(authUser, [1])

    let sending = false
    let form = useForm({
        semillero_investigacion_id: semilleroInvestigacion?.id,
        institucion_educativa_id: institucionEducativa ? institucionEducativa.id : null,
        name: '',
        email: '',
        password: null,
        password_confirmation: null,
        tipo_documento: '',
        numero_documento: '',
        numero_celular: '',
        intereses: '',
        cv: null,
        perfil: '',
        cvlac: '',
        esta_habilitado: true,
        rol_id: null,
        autorizacion_tratamiento_datos: true,
    })

    function submit() {
        if (isSuperAdmin || checkPermission(authUser, [17, 23])) {
            $form.post(route('users.store'), {
                onStart: () => (sending = true),
                onFinish: () => (sending = false),
            })
        }
    }

    let formEmpresario = useForm({
        name: '',
        email: '',
        password: null,
        password_confirmation: null,
        tipo_documento: '',
        numero_documento: '',
        numero_celular: '',
        nit: '',
        nombre_empresa: '',
        direccion_empresa: '',
        numero_celular_empresa: '',
        email_empresa: '',
        autorizacion_tratamiento_datos: true,
        esta_habilitado: true,
        rol_id: null,
    })

    function submitEmpresario() {
        if (isSuperAdmin || checkPermission(authUser, [17, 23])) {
            if ($formEmpresario.autorizacion_tratamiento_datos) {
                $formEmpresario.post(route('users.empresario.register'))
            }
        }
    }
</script>

<AuthenticatedLayout>
    <header slot="header" class="bg-white">
        <div class="flex items-center justify-between max-w-7xl mx-auto py-6">
            <div>
                <h1>
                    <a use:inertia href={route('users.index', ['institucionEducativaId=' + institucionEducativa?.id])} class="text-gray-500 hover:text-gray-600"> Usuarios </a>
                    <span class="text-gray-500 font-medium">/</span>
                    Crear
                </h1>
            </div>
        </div>
    </header>

    <div class="grid grid-cols-3 gap-6 my-20">
        <div class="col-span-1">
            <h3 class="text-lg font-medium leading-6 text-gray-900">Información del usuario</h3>
            <p class="mt-1 text-sm text-gray-600">Ingrese la siguiente información para crear un nuevo usuario.</p>
        </div>
        <div class="bg-white rounded shadow col-span-2">
            <InfoMessage>
                Por favor seleccione el tipo de usuario:
                <div class="grid grid-cols-2 gap-2 mt-4">
                    <Button on:click={() => (tipoUsuario = 'rredsi')} variant={tipoUsuario == 'rredsi' ? 'raised' : 'outlined'} class="p-2 w-full">Usuario RREDSI</Button>

                    <Button on:click={() => (tipoUsuario = 'empresario')} variant={tipoUsuario == 'empresario' ? 'raised' : 'outlined'} class="p-2 w-full">Empresario</Button>
                </div>
            </InfoMessage>

            {#if tipoUsuario == 'rredsi'}
                <Form {errors} {authUser} {submit} {tiposDocumento} {roles} {institucionEducativa} method="create" {institucionesEducativas} {sending} {form} />
            {:else if tipoUsuario == 'empresario'}
                <FormEmpresario {errors} authUser={null} submit={submitEmpresario} {tiposDocumento} {roles} method="register" {sending} {formEmpresario} />
            {/if}
        </div>
    </div>
</AuthenticatedLayout>
