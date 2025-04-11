local teleport_table = {
location1 = Vector3.new(0, 7, 0), -- Primeira parada (subida)
location2 = Vector3.new(0, 3, 0)   -- Destino final (chão)
}

local tween_s = game:GetService('TweenService')
local tweeninfo = TweenInfo.new(2, Enum.EasingStyle.Linear)

local lp = game.Players.LocalPlayer
local button = script.Parent:FindFirstChild("TextButton") -- Referência ao botão

function bypass_teleport(v, callback)
if lp.Character and lp.Character:FindFirstChild('HumanoidRootPart') then
local cf = CFrame.new(v)
local a = tween_s:Create(lp.Character.HumanoidRootPart, tweeninfo, {CFrame = cf})
a:Play()

-- Quando a animação terminar, chamar o próximo movimento (se houver)    
    a.Completed:Connect(function()    
        if callback then callback() end    
    end)    
end

end

-- Conectar o botão ao evento de clique
if button then
button.MouseButton1Click:Connect(function()
print("Botão pressionado! Iniciando voo...")
bypass_teleport(teleport_table.location1, function()
bypass_teleport(teleport_table.location2)
end)
end)
else
warn("Botão TextButton não encontrado!")
end

