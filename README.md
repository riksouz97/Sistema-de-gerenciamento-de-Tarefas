<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sistema de Gerenciamento de Tarefas</title>

  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
  <nav class="navbar navbar-expand-sm bg-light">
    <div class="container-fluid">
      <span class="navbar-brand">
        Sistema de Gerenciamento de Tarefas
      </span>
      <button class="btn btn-primary"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#collapsibleForm">
        + Nova Tarefa
      </button>
    </div>
  </nav>
  <div class="container my-4">
    <div class="collapse" id="collapsibleForm">
      <div class="card card-body mb-4">
        <form id="formTarefa" class="row g-3">
          <!-- Título -->
          <div class="col-12">
            <label for="tituloTarefa" class="form-label">Título da tarefa</label>
            <input type="text"
              class="form-control"
              id="tituloTarefa"
              required>
          </div>
          <!-- Descrição -->
          <div class="col-12">
            <label for="descricaoTarefa" class="form-label">Descrição da tarefa</label>
            <textarea class="form-control"
              id="descricaoTarefa"
              rows="3"
              placeholder="Digite a descrição"></textarea>
          </div>
          <div class="col-12">
            <button type="submit" class="btn btn-success">
              Adicionar
            </button>
            <button type="button"
              class="btn btn-secondary"
              data-bs-toggle="collapse"
              data-bs-target="#collapsibleForm">
              Cancelar
            </button>
          </div>
        </form>
      </div>
    </div>
    <!-- Kanban -->
    <div class="row g-3">
      <!-- A Fazer -->
      <div class="col-md-4">
        <div class="card">
          <div class="card-header bg-primary text-white">
            <h5 class="mb-0">A Fazer</h5>
          </div>
          <div class="card-body" id="colAfazer">
            <p class="text-muted">Nenhuma tarefa ainda.</p>
          </div>
        </div>
      </div>
      <!-- Em Progresso -->
      <div class="col-md-4">
        <div class="card">
          <div class="card-header bg-warning">
            <h5 class="mb-0">Em Progresso</h5>
          </div>
          <div class="card-body" id="colEmProgresso">
            <p class="text-muted">Nenhuma tarefa ainda.</p>
          </div>
        </div>
      </div>
      <!-- Concluído -->
      <div class="col-md-4">
        <div class="card">
          <div class="card-header bg-success text-white">
            <h5 class="mb-0">Concluído</h5>
          </div>
          <div class="card-body" id="colConcluido">
            <p class="text-muted">Nenhuma tarefa ainda.</p>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- Bootstrap -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"></script>
  <script>
    (function () {
      var form = document.getElementById('formTarefa');
      var colAfazer = document.getElementById('colAfazer');
      var colEmProgresso = document.getElementById('colEmProgresso');
      var colConcluido = document.getElementById('colConcluido');
      var collapseEl = document.getElementById('collapsibleForm');
      var collapse = new bootstrap.Collapse(collapseEl, { toggle: false });
      function removerMensagem(coluna) {
        var msg = coluna.querySelector('.text-muted');
        if (msg) msg.remove();
      }
      function adicionarMensagem(coluna) {
        if (coluna.children.length === 0) {
          coluna.innerHTML = '<p class="text-muted">Nenhuma tarefa ainda.</p>';
        }
      }
      // Criar tarefa
      function criarCard(titulo, descricao) {
        var card = document.createElement('div');
        card.className = 'card mb-2';
        card.innerHTML = `
          <div class="card-body">
            <h6 class="mb-1">${titulo}</h6>
            <p class="text-muted mb-2">${descricao || ''}</p>
            <div class="d-flex justify-content-between">
              <button class="btn btn-sm btn-secondary voltar">
                ← Voltar
              </button>
              <div class="d-flex gap-2">
                <button class="btn btn-sm btn-danger excluir">
                  Excluir Tarefa
                </button>
                <button class="btn btn-sm btn-primary avancar">
                  Avançar →
                </button>
              </div>
            </div>
          </div>
        `;
        return card;
      }
      // Adicionar tarefa
      form.addEventListener('submit', function (e) {
        e.preventDefault();
        var titulo = document.getElementById('tituloTarefa').value.trim();
        var descricao = document.getElementById('descricaoTarefa').value.trim();
        if (titulo === '') return;
        removerMensagem(colAfazer);
        var card = criarCard(titulo, descricao);
        colAfazer.appendChild(card);
        form.reset();
        collapse.hide();
      });
      // Ações
      document.addEventListener('click', function (e) {
        var card = e.target.closest('.card');
        if (!card) return;
        var colunaAtual = card.parentElement;
        // Excluir
        if (e.target.classList.contains('excluir')) {
          if (confirm('Deseja realmente excluir esta tarefa?')) {
            card.remove();
            adicionarMensagem(colunaAtual);
          }
          return;
        }
        // Avançar
        if (e.target.classList.contains('avancar')) {
          var proximaColuna;
          if (colunaAtual.id === 'colAfazer') {
            proximaColuna = colEmProgresso;
          }
          else if (colunaAtual.id === 'colEmProgresso') {
            proximaColuna = colConcluido;
          }
          else return;
          removerMensagem(proximaColuna);
          proximaColuna.appendChild(card);
          adicionarMensagem(colunaAtual);
        }
        // Voltar
        if (e.target.classList.contains('voltar')) {
          var colunaAnterior;
          if (colunaAtual.id === 'colConcluido') {
            colunaAnterior = colEmProgresso;
          }
          else if (colunaAtual.id === 'colEmProgresso') {
            colunaAnterior = colAfazer;
          }
          else return;
          removerMensagem(colunaAnterior);
          colunaAnterior.appendChild(card);
          adicionarMensagem(colunaAtual);
        }
      });
    })();
  </script>
</body>
</html>
