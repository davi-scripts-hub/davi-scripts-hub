
repeat task.wait() until game:IsLoaded()

local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()

player.CharacterAdded:Connect(function(c)
    char = c
end)

local Toggles = {
    Farm = false,
    Chest = false
}

local FarmMode = "Melee"


local ScreenGui = Instance.new("ScreenGui", game.CoreGui)

local OpenButton = Instance.new("TextButton")
OpenButton.Parent = ScreenGui
OpenButton.Size = UDim2.new(0,60,0,60)
OpenButton.Position = UDim2.new(0,100,0,100)
OpenButton.Text = "DAVI Scripts"
OpenButton.BackgroundColor3 = Color3.fromRGB(20,20,20)
OpenButton.TextColor3 = Color3.new(1,1,1)
OpenButton.Active = true
OpenButton.Draggable = true

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0,300,0,350)
Frame.Position = UDim2.new(0.3,0,0.2,0)
Frame.BackgroundColor3 = Color3.fromRGB(15,15,15)
Frame.Visible = false
Frame.Active = true
Frame.Draggable = true

Instance.new("UIListLayout", Frame)

OpenButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)


function criarBotao(texto, callback)
    local btn = Instance.new("TextButton")
    btn.Parent = Frame
    btn.Size = UDim2.new(1,0,0,35)
    btn.Text = texto
    btn.BackgroundColor3 = Color3.fromRGB(30,30,30)
    btn.TextColor3 = Color3.new(1,1,1)

    btn.MouseButton1Click:Connect(callback)
end


function Equipar()
    pcall(function()
        for _,v in pairs(player.Backpack:GetChildren()) do
            if v:IsA("Tool") then
                if FarmMode == "Melee" and v.Name:lower():find("combat") then
                    char.Humanoid:EquipTool(v)
                elseif FarmMode == "Sword" then
                    char.Humanoid:EquipTool(v)
                elseif FarmMode == "Gun" then
                    char.Humanoid:EquipTool(v)
                elseif FarmMode == "Fruit" then
                    char.Humanoid:EquipTool(v)
                end
            end
        end
    end)
end

function Attack()
    pcall(function()
        local tool = char:FindFirstChildOfClass("Tool")
        if tool then
            tool:Activate()
        end
    end)
end

local BV

function FlyTo(pos)
    if not char:FindFirstChild("HumanoidRootPart") then return end

    if not BV then
        BV = Instance.new("BodyVelocity")
        BV.MaxForce = Vector3.new(999999,999999,999999)
        BV.Parent = char.HumanoidRootPart
    end

    BV.Velocity = (pos - char.HumanoidRootPart.Position).Unit * 60
end


function AutoFarm()
    while Toggles.Farm do
        task.wait(0.1)

        pcall(function()
            local enemies = workspace:FindFirstChild("Enemies")
            if not enemies then return end

            for _,mob in pairs(enemies:GetChildren()) do
                if mob:FindFirstChild("Humanoid") 
                and mob:FindFirstChild("HumanoidRootPart")
                and mob.Humanoid.Health > 0 then

                    repeat task.wait(0.1)
                        if not Toggles.Farm then break end

                        Equipar()

                        FlyTo(mob.HumanoidRootPart.Position + Vector3.new(0,15,0))

                        Attack()

                    until mob.Humanoid.Health <= 0 or not Toggles.Farm
                end
            end
        end)
    end
end

function AutoChest()
    while Toggles.Chest do
        task.wait(2)

        for _,v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and string.find(v.Name:lower(),"chest") then
                if char:FindFirstChild("HumanoidRootPart") then
                    char.HumanoidRootPart.CFrame = v.CFrame
                    task.wait(0.5)
                end
            end
        end
    end
end

criarBotao("Auto Farm", function()
    Toggles.Farm = not Toggles.Farm
    if Toggles.Farm then task.spawn(AutoFarm) end
end)

criarBotao("Auto Chest", function()
    Toggles.Chest = not Toggles.Chest
    if Toggles.Chest then task.spawn(AutoChest) end
end)

criarBotao("Modo: Melee", function() FarmMode = "Melee" end)
criarBotao("Modo: Sword", function() FarmMode = "Sword" end)
criarBotao("Modo: Fruit", function() FarmMode = "Fruit" end)
criarBotao("Modo: Gun", function() FarmMode = "Gun" end)

criarBotao("Fechar", function()
    ScreenGui:Destroy()
end)
