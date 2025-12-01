--[[
 Brookhaven Spammar Script usando TextChatService - "🗼LEST"
 - Menu preto, borda roxa, cantos arredondados, fonte grande
 - Título "🗼LEST" centralizado
 - Botão único abrir/fechar: círculo preto, arrastável, SEM IMAGEM
 - Menu começa FECHADO!
 - "Ativar Botões" e "Desativar Botões" mais para cima do rodapé (-60)
 - Flutuantes M/R/F, brancos, arrastáveis, SEM X
 - Delay decimal, mínimo 0.1s
--]]

local TextChatService = game:GetService("TextChatService")
local UserInputService = game:GetService("UserInputService")
local player = game.Players.LocalPlayer

local comandos = {"/mat", "/render", "/fura pneu"}
local iniciais = {["/mat"] = "M", ["/render"] = "R", ["/fura pneu"] = "F"}
local delays = {["/mat"] = 0.2, ["/render"] = 0.2, ["/fura pneu"] = 0.2}
local reps = {["/mat"] = 1, ["/render"] = 1, ["/fura pneu"] = 1}
local spamThreads, spamON, mainGui = {}, {}, nil
local actionBtnsGui = nil

-- Arrastável hiper sensível
local function makeDraggableButton(btn)
    local dragging, dragStart, startPos
    btn.Active = true
    btn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = btn.Position
            btn.BackgroundTransparency = 0.15
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            btn.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        dragging = false
        btn.BackgroundTransparency = 0
    end)
end

local function spammarComando(comando)
    spamON[comando] = true
    local delay = tonumber(delays[comando]) or 0.2
    if delay < 0.1 then delay = 0.1 end
    local repeticoes = tonumber(reps[comando]) or 1
    local channel = (TextChatService.TextChannels and TextChatService.TextChannels:FindFirstChild("RBXGeneral"))
        or (TextChatService:FindFirstChild("TextChannels") and TextChatService:FindFirstChild("TextChannels"):FindFirstChild("RBXGeneral"))
        or TextChatService:FindFirstChild("RBXGeneral")
        or TextChatService:FindFirstChild("General")
        or TextChatService:FindFirstChild("All")
    spamThreads[comando] = task.spawn(function()
        for i = 1, repeticoes do
            if channel and spamON[comando] then
                channel:SendAsync(comando)
            end
            task.wait(delay)
        end
        spamON[comando] = false
    end)
end
local function pararSpam(comando) spamON[comando] = false end

-- Flutuantes
local function showActionButtons()
    if actionBtnsGui then actionBtnsGui:Destroy() end
    actionBtnsGui = Instance.new("ScreenGui")
    actionBtnsGui.Parent = player:WaitForChild("PlayerGui")
    actionBtnsGui.Name = "ActionBtns"
    local cores = {
        [1] = Color3.fromRGB(255,99,99), -- M
        [2] = Color3.fromRGB(120,160,255), -- R
        [3] = Color3.fromRGB(120,255,160), -- F
    }
    for i,cmd in ipairs(comandos) do
        local letra = iniciais[cmd] or string.sub(string.upper(cmd), 2, 2)
        local btn = Instance.new("TextButton")
        btn.Parent = actionBtnsGui
        btn.Size = UDim2.new(0,54,0,54)
        btn.Position = UDim2.new(0, 16 + (i-1)*70, 1, -120)
        btn.BackgroundColor3 = cores[i]
        btn.BackgroundTransparency = 0.2
        btn.Text = letra
        btn.Font = Enum.Font.GothamBold
        btn.TextSize = 40
        btn.TextColor3 = Color3.fromRGB(255,255,255)
        btn.Name = "Btn_"..cmd
        local corner = Instance.new("UICorner", btn) corner.CornerRadius = UDim.new(0.5,0)
        local stroke = Instance.new("UIStroke", btn)
        stroke.Color = Color3.fromRGB(140, 20, 252)
        stroke.Thickness = 2
        btn.MouseButton1Click:Connect(function() spammarComando(cmd) end)
        makeDraggableButton(btn)
    end
end

local function hideActionButtons()
    if actionBtnsGui then actionBtnsGui:Destroy() actionBtnsGui = nil end
end

-- MENU
local function createMainGui()
    local gui = Instance.new("ScreenGui")
    gui.Name = "Spammar"
    gui.Parent = player:WaitForChild("PlayerGui")
    mainGui = gui

    local frame = Instance.new("Frame", gui)
    frame.Size = UDim2.new(0, 320, 0, 210 + #comandos*90)
    frame.Position = UDim2.new(0.5, -160, 0.5, -((210 + #comandos*90)//2))
    frame.BackgroundColor3 = Color3.fromRGB(0,0,0)
    local uicorner = Instance.new("UICorner", frame) uicorner.CornerRadius = UDim.new(0.15,0)
    local uistroke = Instance.new("UIStroke", frame)
    uistroke.Color = Color3.fromRGB(140, 20, 252) uistroke.Thickness = 4

    -- Título
    local titlebox = Instance.new("Frame", frame)
    titlebox.Size = UDim2.new(0.7,0,0,52)
    titlebox.Position = UDim2.new(0.15,0,0,10)
    titlebox.BackgroundColor3 = Color3.fromRGB(20,0,34) titlebox.BackgroundTransparency = 0.1
    local tcorner = Instance.new("UICorner", titlebox) tcorner.CornerRadius = UDim.new(0.7,0)
    local tstroke = Instance.new("UIStroke", titlebox)
    tstroke.Color = Color3.fromRGB(140, 20, 252) tstroke.Thickness = 3
    local titulo = Instance.new("TextLabel", titlebox)
    titulo.Text = "🗼LEST"
    titulo.Size = UDim2.new(1, 0, 1, 0)
    titulo.Font = Enum.Font.GothamBold
    titulo.TextSize = 39 titulo.TextColor3 = Color3.new(1,1,1) titulo.BackgroundTransparency = 1

    for i,cmd in ipairs(comandos) do
        local yBase = 75 + (i-1)*90

        local lbl = Instance.new("TextLabel", frame)
        lbl.Text = cmd
        lbl.Size = UDim2.new(0.7, 0, 0, 37)
        lbl.Position = UDim2.new(0.05,0,0,yBase+5)
        lbl.BackgroundTransparency = 1
        lbl.Font = Enum.Font.GothamBold
        lbl.TextSize = 24
        lbl.TextColor3 = Color3.fromRGB(255,255,255)
        lbl.TextXAlignment = Enum.TextXAlignment.Left

        local delayBox = Instance.new("TextBox", frame)
        delayBox.Size = UDim2.new(0.4, 0, 0, 31)
        delayBox.Position = UDim2.new(0.05,0,0,yBase+42)
        delayBox.Text = tostring(delays[cmd])
        delayBox.PlaceholderText = "Delay"
        delayBox.Font = Enum.Font.GothamBold
        delayBox.TextSize = 19
        delayBox.BackgroundColor3 = Color3.fromRGB(24,0,66)
        delayBox.TextColor3 = Color3.fromRGB(255,255,255)
        local inputCorner1 = Instance.new("UICorner", delayBox)
        inputCorner1.CornerRadius = UDim.new(0.32,0)
        delayBox.FocusLost:Connect(function()
            delays[cmd] = tonumber(delayBox.Text) or 0.2
            if delays[cmd] < 0.1 then delays[cmd] = 0.1 end
            delayBox.Text = tostring(delays[cmd])
        end)

        local repsBox = Instance.new("TextBox", frame)
        repsBox.Size = UDim2.new(0.4, 0, 0, 31)
        repsBox.Position = UDim2.new(0.55,0,0,yBase+42)
        repsBox.Text = tostring(reps[cmd])
        repsBox.PlaceholderText = "Repetições"
        repsBox.Font = Enum.Font.GothamBold
        repsBox.TextSize = 19
        repsBox.BackgroundColor3 = Color3.fromRGB(24,0,66)
        repsBox.TextColor3 = Color3.fromRGB(255,255,255)
        local inputCorner2 = Instance.new("UICorner", repsBox)
        inputCorner2.CornerRadius = UDim.new(0.32,0)
        repsBox.FocusLost:Connect(function()
            reps[cmd] = math.max(1, math.floor(tonumber(repsBox.Text) or 1))
            repsBox.Text = tostring(reps[cmd])
        end)

        local startBtn = Instance.new("TextButton", frame)
        startBtn.Text = "INICIAR"
        startBtn.Size = UDim2.new(0.4,0,0,30)
        startBtn.Position = UDim2.new(0.05,0,0,yBase+77)
        startBtn.BackgroundColor3 = Color3.fromRGB(40,130,40)
        startBtn.TextColor3 = Color3.fromRGB(255,255,255)
        startBtn.Font = Enum.Font.GothamBold
        startBtn.TextSize = 19
        local btnStartCorner = Instance.new("UICorner", startBtn)
        btnStartCorner.CornerRadius = UDim.new(0.4,0)
        startBtn.MouseButton1Click:Connect(function()
            pararSpam(cmd)
            spammarComando(cmd)
        end)

        local stopBtn = Instance.new("TextButton", frame)
        stopBtn.Text = "PARAR"
        stopBtn.Size = UDim2.new(0.4,0,0,30)
        stopBtn.Position = UDim2.new(0.55,0,0,yBase+77)
        stopBtn.BackgroundColor3 = Color3.fromRGB(150,40,140)
        stopBtn.TextColor3 = Color3.fromRGB(255,255,255)
        stopBtn.Font = Enum.Font.GothamBold
        stopBtn.TextSize = 19
        local btnStopCorner = Instance.new("UICorner", stopBtn)
        btnStopCorner.CornerRadius = UDim.new(0.4,0)
        stopBtn.MouseButton1Click:Connect(function() pararSpam(cmd) end)
    end

    -- Botões ativar/desativar mais para cima do rodapé (-60)
    local botaoAtivar = Instance.new("TextButton", frame)
    botaoAtivar.Text = "Ativar Botões"
    botaoAtivar.Size = UDim2.new(0.36,0,0,36)
    botaoAtivar.Position = UDim2.new(0.1,0,1,-60)
    botaoAtivar.BackgroundColor3 = Color3.fromRGB(80,80,180)
    botaoAtivar.TextColor3 = Color3.fromRGB(255,255,255)
    botaoAtivar.Font = Enum.Font.GothamBold
    botaoAtivar.TextSize = 19
    local bcorner = Instance.new("UICorner", botaoAtivar)
    bcorner.CornerRadius = UDim.new(0.5,0)
    botaoAtivar.MouseButton1Click:Connect(showActionButtons)

    local botaoDesativar = Instance.new("TextButton", frame)
    botaoDesativar.Text = "Desativar Botões"
    botaoDesativar.Size = UDim2.new(0.36,0,0,36)
    botaoDesativar.Position = UDim2.new(0.54,0,1,-60)
    botaoDesativar.BackgroundColor3 = Color3.fromRGB(150,20,60)
    botaoDesativar.TextColor3 = Color3.fromRGB(255,255,255)
    botaoDesativar.Font = Enum.Font.GothamBold
    botaoDesativar.TextSize = 19
    local dcorner = Instance.new("UICorner", botaoDesativar)
    dcorner.CornerRadius = UDim.new(0.5,0)
    botaoDesativar.MouseButton1Click:Connect(hideActionButtons)
end

-- Botão único abrir/fechar menu (círculo preto, arrastável)
local function createMenuToggleBtn()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "MenuToggleBtn"
    screenGui.Parent = player:WaitForChild("PlayerGui")

    local btn = Instance.new("ImageButton", screenGui)
    btn.Size = UDim2.new(0, 44, 0, 44)
    btn.Position = UDim2.new(0, 16, 1, -56)
    btn.BackgroundColor3 = Color3.fromRGB(0,0,0)
    btn.BackgroundTransparency = 0
    btn.Name = "ToggleMenuBtn"
    btn.Image = "" -- sem imagem

    local uicorner = Instance.new("UICorner", btn)
    uicorner.CornerRadius = UDim.new(0.5,0)
    local uistroke = Instance.new("UIStroke", btn)
    uistroke.Color = Color3.fromRGB(140, 20, 252)
    uistroke.Thickness = 2

    -- Arrastável sensível
    makeDraggableButton(btn)

    btn.MouseButton1Click:Connect(function()
        if mainGui then
            mainGui.Enabled = not mainGui.Enabled
        else
            createMainGui()
            mainGui.Enabled = true
        end
    end)
end

createMainGui()
createMenuToggleBtn()
mainGui.Enabled = false -- começa fechado!
