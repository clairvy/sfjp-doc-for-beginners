blog�`���[�g���A��(4) �ǉ��̐ݒ�
================================

CSRF�V�[�N���b�g�̕ύX
----------------------

symfony�ł̓f�t�H���g��CSRF�΍􂪗L���ɂȂ��Ă��܂��isymfony 1.3���B����ȑO�̓I�v�V�����j�B
�T���h�{�b�N�X�p�b�P�[�W���_�E�����[�h�����ꍇ�́A����CSRF�ی�Ɏg����V�[�N���b�g�������K���ύX���Ă��������B
apps/frontend/config/settings.yml�t�@�C�����J���A**csrf_secret**�̒l��K���Ȕ��p�p��������ɏ��������Ă��������B

<pre>
all:
  .settings:
    # Form security secret (CSRF protection)
    csrf_secret:            fweefawkpkfwea230912jfasmknfa923 # �K���ȕ�����
</pre>


�f�B���N�g���̃p�[�~�b�V����
----------------------------

Linux���̏ꍇ�́A�������̃f�B���N�g���̃p�[�~�b�V�������E�F�u�T�[�o�[�̃v���Z�X���珑�����߂�悤�ɐݒ肷��K�v������܂��B
������s���ɂ́A�ȉ��̂悤��symfony�R�}���h�����s���Ă��������B

<pre>
php symfony project:permissions
</pre>

> **TIP**
> ��̓I�ɂ́Acache/�����log/�f�B���N�g�����������݉\�ȃp�[�~�b�V�����ɐݒ肵�܂��B
