<script>
    import AuthenticatedLayout, { title } from '@/Layouts/Authenticated'
    import { inertia, useForm, page } from '@inertiajs/inertia-svelte'
    import { route, checkRole, checkPermission } from '@/Utils'
    import { _ } from 'svelte-i18n'

    import Input from '@/Shared/Input'
    import LoadingButton from '@/Shared/LoadingButton'

    export let errors

    $: $title = 'Soporte'

    /**
     * Permisos
     */
    let authUser = $page.props.auth.user
    let isSuperAdmin = checkRole(authUser, [1])

    let sending = false
    let form = useForm({
        old_password: '',
        password: '',
        password_confirmation: '',
    })

    function submit() {
        $form.put(route('users.change-password'), {
            onStart: () => (sending = true),
            onFinish: () => (sending = false),
        })
    }
</script>

<AuthenticatedLayout>
    <div class="grid grid-cols-3 gap-6 my-20">
        <div class="col-span-1">
            <h3 class="text-lg font-medium leading-6 text-gray-900">Cambiar contraseña</h3>
            <p class="mt-1 text-sm text-gray-600">Ingrese la contraseña actual y luego genere una nueva contraseña.</p>
        </div>
        <div class="bg-white rounded shadow col-span-2">
            <form on:submit|preventDefault={submit}>
                <fieldset class="p-8">
                    <div class="mt-4">
                        <Input label="Contraseña actual" id="old_password" type="password" class="mt-1" bind:value={$form.old_password} error={errors.old_password} required />
                    </div>
                    <div class="mt-4">
                        <Input label={'Nueva ' + $_('Password').toLowerCase()} id="password" type="password" class="mt-1" bind:value={$form.password} error={errors.password} required autocomplete="new-password" />
                    </div>

                    <div class="mt-4">
                        <Input label={$_('Confirm Password')} id="password_confirmation" type="password" class="mt-1" bind:value={$form.password_confirmation} error={errors.password_confirmation} required autocomplete="new-password" />
                    </div>
                </fieldset>
                <div class="px-8 py-4 bg-gray-100 border-t border-gray-200 flex items-center sticky bottom-0">
                    <LoadingButton bind:loading={sending} class="btn-indigo" type="submit" bind:disabled={$form.autorizacion_datos}>Cambiar contraseña</LoadingButton>
                </div>
            </form>
        </div>
    </div>
</AuthenticatedLayout>
