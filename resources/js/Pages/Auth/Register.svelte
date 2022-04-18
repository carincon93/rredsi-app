<script>
    import GuestLayout from '@/Layouts/Guest'
    import { useForm } from '@inertiajs/inertia-svelte'
    import { route, checkRole, checkPermission } from '@/Utils'
    import { _ } from 'svelte-i18n'
    import { Inertia } from '@inertiajs/inertia'

    import FormEstudiante from '../Users/Form'
    import FormEmpresario from '../Users/FormEmpresario'
    import InfoMessage from '@/Shared/InfoMessage'
    import Button from '@/Shared/Button'

    export let errors
    export let institucionesEducativas
    export let tipoUsuario

    let tiposDocumento = [
        { value: 'cc', label: 'Cédula de ciudadanía' },
        { value: 'ti', label: 'Tarjeta de identidad' },
        { value: 'ce', label: 'Cédula de extranjería' },
    ]

    let sending = false

    let formEstudiante = useForm({
        institucion_educativa_id: null,
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
        autorizacion_tratamiento_datos: true,
        tipo_usuario: tipoUsuario,
    })

    function submitEstudiante() {
        if ($formEstudiante.autorizacion_tratamiento_datos) {
            $formEstudiante.post(route('register'))
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
        tipo_usuario: tipoUsuario,
    })

    function submitEmpresario() {
        if ($formEmpresario.autorizacion_tratamiento_datos) {
            $formEmpresario.post(route('register'))
        }
    }

    document.querySelector('body').classList.contains('overflow-hidden') ? document.querySelector('body').classList.remove('overflow-hidden') : null
</script>

<GuestLayout class="mt-20">
    <InfoMessage>
        Por favor seleccione el tipo de usuario:
        <div class="grid grid-cols-2 gap-2 mt-4">
            <Button on:click={() => Inertia.visit(route('register', 'tipoUsuario=estudiante'))} variant={tipoUsuario == 'estudiante' ? 'raised' : 'outlined'} class="p-2 w-full">Estudiante</Button>

            <Button on:click={() => Inertia.visit(route('register', 'tipoUsuario=empresario'))} variant={tipoUsuario == 'empresario' ? 'raised' : 'outlined'} class="p-2 w-full">Empresario</Button>
        </div>
    </InfoMessage>
    <div>
        {#if tipoUsuario == 'estudiante'}
            <FormEstudiante {errors} authUser={null} submit={submitEstudiante} {tiposDocumento} roles={null} method="register" {institucionesEducativas} {sending} form={formEstudiante} />
        {:else if tipoUsuario == 'empresario'}
            <FormEmpresario {errors} authUser={null} submit={submitEmpresario} {tiposDocumento} roles={null} method="register" {sending} {formEmpresario} />
        {/if}
    </div>
</GuestLayout>
