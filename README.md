local teleport_table = {  
    location1 = Vector3.new(0, 7, 0), -- Primeira parada (subida)  
    location2 = Vector3.new(0, 3, 0)   -- Destino final (chão)  
}  

local tween_s = game:GetService('TweenService')  
local base_speed = 100 -- Velocidade em studs por segundo  

local lp = game.Players.LocalPlayer  
local button = script.Parent:FindFirstChild("TextButton") -- Referência ao botão  

local isTeleporting = false -- Evitar sobreposição de animações

function bypass_teleport(v, callback)  
    local character = lp.Character
    if character and character:FindFirstChild('HumanoidRootPart') then  
        local rootPart = character.HumanoidRootPart
        
        -- Calcular a distância e ajustar o tempo
        local distance = (rootPart.Position - v).Magnitude
        local duration = distance / base_speed -- Tempo baseado na velocidade fixa
        
        local tweeninfo = TweenInfo.new(duration, Enum.EasingStyle.Linear)  
        local cf = CFrame.new(v)  
        local a = tween_s:Create(rootPart, tweeninfo, {CFrame = cf})  
        a:Play()  

        -- Quando a animação terminar, chamar o próximo movimento (se houver)  
        a.Completed:Once(function()  
            if callback then  
                callback()  
            else  
                isTeleporting = false -- Resetar estado  
            end  
        end)  
    else  
        warn("HumanoidRootPart não encontrado!")  
        isTeleporting = false  
    end  
end  

-- Conectar o botão ao evento de clique  
if button then  
    button.MouseButton1Click:Connect(function()  
        if isTeleporting then return end -- Evita múltiplos cliques simultâneos  
        isTeleporting = true  

        print("Botão pressionado! Iniciando voo...")  
        bypass_teleport(teleport_table.location1, function()  
            bypass_teleport(teleport_table.location2)  
        end)  
    end)  
else  
    warn("Botão TextButton não encontrado!")  
end  

-- Atualizar personagem quando for recriado  
lp.CharacterAdded:Connect(function(char)  
    isTeleporting = false -- Resetar estado ao renascer  
end)
