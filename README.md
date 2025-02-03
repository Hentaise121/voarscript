-- Criar um painel de interface gráfica (GUI)
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local FlightButton = Instance.new("TextButton")
local SpeedBox = Instance.new("TextBox")
local StatusLabel = Instance.new("TextLabel")

-- Configuração da GUI
ScreenGui.Parent = game:GetService("CoreGui") -- Para funcionar em qualquer jogo
Frame.Parent = ScreenGui
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.8, 0, 0.2, 0)
Frame.BackgroundColor3 = Color3.new(0, 0, 0.2)
Frame.Active = true
Frame.Draggable = true

-- Botão para ativar/desativar voo
FlightButton.Parent = Frame
FlightButton.Size = UDim2.new(0, 180, 0, 50)
FlightButton.Position = UDim2.new(0, 10, 0, 10)
FlightButton.Text = "Ativar Voo"
FlightButton.BackgroundColor3 = Color3.new(0, 1, 0)

-- Caixa de entrada para velocidade
SpeedBox.Parent = Frame
SpeedBox.Size = UDim2.new(0, 180, 0, 30)
SpeedBox.Position = UDim2.new(0, 10, 0, 70)
SpeedBox.Text = "Velocidade"
SpeedBox.BackgroundColor3 = Color3.new(1, 1, 1)

-- Status do voo
StatusLabel.Parent = Frame
StatusLabel.Size = UDim2.new(0, 180, 0, 30)
StatusLabel.Position = UDim2.new(0, 10, 0, 110)
StatusLabel.Text = "Status: Desligado"
StatusLabel.BackgroundColor3 = Color3.new(0.5, 0.5, 0.5)

-- Variáveis do voo
local flying = false
local speed = 50
local player = game:GetService("Players").LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humanoid = char:FindFirstChildOfClass("Humanoid")

-- Função para ativar/desativar voo
local function toggleFlight()
    if flying then
        flying = false
        FlightButton.Text = "Ativar Voo"
        FlightButton.BackgroundColor3 = Color3.new(0, 1, 0)
        StatusLabel.Text = "Status: Desligado"
        humanoid:ChangeState(Enum.HumanoidStateType.Physics)
    else
        flying = true
        FlightButton.Text = "Desativar Voo"
        FlightButton.BackgroundColor3 = Color3.new(1, 0, 0)
        StatusLabel.Text = "Status: Ligado"
        
        -- Criar loop para manter o jogador no ar
        spawn(function()
            local hrp = char:FindFirstChild("HumanoidRootPart")
            while flying and hrp do
                hrp.Velocity = Vector3.new(0, speed, 0)
                wait(0.1)
            end
        end)
    end
end

-- Atualizar velocidade ao digitar na caixa de texto
SpeedBox.FocusLost:Connect(function()
    local newSpeed = tonumber(SpeedBox.Text)
    if newSpeed then
        speed = newSpeed
    else
        SpeedBox.Text = "Número inválido"
    end
end)

-- Conectar botão ao voo
FlightButton.MouseButton1Click:Connect(toggleFlight)
