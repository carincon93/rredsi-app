<script>
    import AuthenticatedLayout, { title } from '@/Layouts/Authenticated'
    import { inertia, useForm, page } from '@inertiajs/inertia-svelte'
    import { route, checkRole, checkPermission } from '@/Utils'
    import { _ } from 'svelte-i18n'

    import Form from './Form'
    import FormEmpresario from './FormEmpresario'

    export let errors
    export let institucionEducativa
    export let user
    export let institucionesEducativas
    export let roles
    export let rolesRelacionados
    export let semilleroInvestigacion

    let tiposDocumento = [
        { value: 'cc', label: 'Cédula de ciudadanía' },
        { value: 'ti', label: 'Tarjeta de identidad' },
        { value: 'ce', label: 'Cédula de extranjería' },
    ]

    $: $title = 'Editar usuario'

    /**
     * Permisos
     */
    let authUser = $page.props.auth.user
    let isSuperAdmin = checkRole(authUser, [1])

    let sending = false
    let form = useForm({
        _method: 'put',
        institucion_educativa_id: institucionEducativa ? institucionEducativa.id : { value: user.institucion_educativa_id, label: institucionesEducativas.find((item) => item.value == user.institucion_educativa_id)?.label },
        semillero_investigacion_id: semilleroInvestigacion?.id,
        name: user.name,
        email: user.email,
        password: '',
        password_confirmation: '',
        tipo_documento: { value: user.tipo_documento, label: tiposDocumento.find((item) => item.value == user.tipo_documento)?.label },
        numero_documento: user.numero_documento,
        numero_celular: user.numero_celular,
        intereses: user.intereses,
        perfil: user.perfil,
        cvlac: user.cvlac,
        cv: null,
        esta_habilitado: user.esta_habilitado,
        autorizacion_tratamiento_datos: user.autorizacion_tratamiento_datos,
        rol_id: rolesRelacionados.length > 0 ? rolesRelacionados : null,
    })

    function submit() {
        if (isSuperAdmin || checkPermission(authUser, [17, 23])) {
            $form.post(route('users.update', user.id), {
                onStart: () => (sending = true),
                onFinish: () => (sending = false),
                preserveScroll: true,
            })
        }
    }

    let formEmpresario = useForm({
        name: user.name,
        email: user.email,
        password: '',
        password_confirmation: '',
        tipo_documento: { value: user.tipo_documento, label: tiposDocumento.find((item) => item.value == user.tipo_documento)?.label },
        numero_documento: user.numero_documento,
        numero_celular: user.numero_celular,
        nit: user.empresa ? user.empresa.nit : '',
        nombre_empresa: user.empresa ? user.empresa.nombre : '',
        direccion_empresa: user.empresa ? user.empresa.direccion : '',
        numero_celular_empresa: user.empresa ? user.empresa.numero_celular : '',
        email_empresa: user.empresa ? user.empresa.email : '',
        autorizacion_tratamiento_datos: user.autorizacion_tratamiento_datos,
        esta_habilitado: user.esta_habilitado,
        rol_id: rolesRelacionados.length > 0 ? rolesRelacionados : null,
    })

    function submitEmpresario() {
        if (isSuperAdmin || checkPermission(authUser, [17, 23])) {
            if ($formEmpresario.autorizacion_tratamiento_datos) {
                $formEmpresario.put(route('users.empresario.update', user.id))
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
                    Editar
                </h1>
            </div>
        </div>
    </header>

    <div class="grid grid-cols-3 gap-6 my-20">
        <div class="col-span-1">
            <h3 class="text-lg font-medium leading-6 text-gray-900">Información del usuario</h3>
            <p class="mt-1 text-sm text-gray-600">Ingrese nueva información para editar el usuario.</p>
        </div>
        <div class="bg-white rounded shadow col-span-2">
            <h1 class="text-2xl text-center">Usuario tipo RREDSI</h1>
            <Form {errors} {authUser} {submit} {tiposDocumento} {roles} {institucionEducativa} {institucionesEducativas} {sending} {form} />

            <hr class="my-20 w-full block" />

            <h1 class="text-2xl text-center">Usuario tipo empresario</h1>
            <FormEmpresario {errors} authUser={null} submit={submitEmpresario} {tiposDocumento} {roles} {sending} {formEmpresario} />
        </div>
    </div>
</AuthenticatedLayout>
